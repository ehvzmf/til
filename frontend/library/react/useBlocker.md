> 📅 Date: 2025-08-06

# 📌 Focus
- React Router v6 `useBlocker` Hook
- 구현: 게시글 작성 중 뒤로 가기 방지 

<br />

# 📝 Learnings

## useBlocker
* React Router v6에서 제공하는 hook
* 사용자가 현재 페이지를 떠나려고 할 때 이를 차단하고 확인 과정을 거칠 수 있게 해주는 기능
* 폼 데이터가 저장되지 않은 상태에서 페이지를 벗어나려 할 때 경고 표시

## 주요 특징
* React Router v6.4+ 에서만 사용 가능
* 브라우저의 기본 beforeunload 이벤트와는 다름 (페이지 새로고침/닫기는 차단 불가)
* SPA 내에서의 라우팅 네비게이션만 차단

## 기본 문법
```javascript
import { useBlocker } from 'react-router-dom';

const blocker = useBlocker(shouldBlock);
```

* `shouldBlock`: boolean 또는 함수 형태로, true일 때 네비게이션을 차단
* 반환값: `blocker` 객체는 `state`, `reset()`, `proceed()` 메서드를 포함

## How to use

### 기본 사용법
```javascript
import React, { useState } from 'react';
import { useBlocker } from 'react-router-dom';

function ContactForm() {
  const [formData, setFormData] = useState({ name: '', email: '' });
  const [isSaved, setIsSaved] = useState(false);
  
  // 폼이 변경되었지만 저장되지 않은 경우에만 차단
  const blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      !isSaved && 
      (formData.name !== '' || formData.email !== '') &&
      currentLocation.pathname !== nextLocation.pathname
  );

  const handleSubmit = (e) => {
    e.preventDefault();
    // 폼 저장 로직
    setIsSaved(true);
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={formData.name}
          onChange={(e) => setFormData({...formData, name: e.target.value})}
          placeholder="이름"
        />
        <input
          type="email"
          value={formData.email}
          onChange={(e) => setFormData({...formData, email: e.target.value})}
          placeholder="이메일"
        />
        <button type="submit">저장</button>
      </form>

      {/* 차단 모달 */}
      {blocker.state === "blocked" && (
        <div className="modal">
          <p>저장되지 않은 변경사항이 있습니다. 정말 페이지를 떠나시겠습니까?</p>
          <button onClick={() => blocker.proceed()}>떠나기</button>
          <button onClick={() => blocker.reset()}>머무르기</button>
        </div>
      )}
    </div>
  );
}
```

### 커스텀 Hook으로 활용
```javascript
// hooks/useUnsavedChanges.js
import { useBlocker } from 'react-router-dom';
import { useEffect, useState } from 'react';

export function useUnsavedChanges(hasUnsavedChanges) {
  const [showModal, setShowModal] = useState(false);
  
  const blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      hasUnsavedChanges && currentLocation.pathname !== nextLocation.pathname
  );

  useEffect(() => {
    if (blocker.state === "blocked") {
      setShowModal(true);
    }
  }, [blocker.state]);

  const handleProceed = () => {
    setShowModal(false);
    blocker.proceed();
  };

  const handleStay = () => {
    setShowModal(false);
    blocker.reset();
  };

  return {
    showModal,
    handleProceed,
    handleStay
  };
}

// 컴포넌트에서 사용
function MyForm() {
  const [isDirty, setIsDirty] = useState(false);
  const { showModal, handleProceed, handleStay } = useUnsavedChanges(isDirty);

  return (
    <>
      <form onChange={() => setIsDirty(true)}>
        {/* 폼 내용 */}
      </form>
      
      {showModal && (
        <ConfirmModal
          onProceed={handleProceed}
          onStay={handleStay}
        />
      )}
    </>
  );
}
```

# impl. 게시글 작성 중 다른 경로로 이동 시 경고 모달 띄우기
- 현재 뒤로 가기 버튼을 통해 글 작성을 완료하지 않고 벗어날 경우에만 모달이 뜨는 상황
- 헤더의 네비게이션 탭을 통해 이동하면 모달 없이 바로 이동
- 새로고침/직접 경로 수정 시 브라우저 자체의 alert가 뜸

<details>
<summary> `useUnsavedChanges` </summary>

```
import { useState, useEffect, useCallback, useRef } from 'react';
import { useNavigate, useLocation, useBlocker } from 'react-router-dom';

interface UseUnsavedChangesProps {
  hasUnsavedChanges: boolean;
  onConfirmLeave?: () => void;
}

export const useUnsavedChanges = ({ hasUnsavedChanges, onConfirmLeave }: UseUnsavedChangesProps) => {
  const [showModal, setShowModal] = useState(false);
  const [pendingNavigation, setPendingNavigation] = useState<(() => void) | null>(null);

  const isLeavingRef = useRef(false);

  // 🔧 React Router v6 네비게이션 차단 (헤더 탭, 브라우저 뒤로가기 등)
  const blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      hasUnsavedChanges && 
      !isLeavingRef.current &&
      currentLocation.pathname !== nextLocation.pathname
  );

  // 🔧 브라우저 새로고침/탭 닫기 감지
  useEffect(() => {
    const handleBeforeUnload = (e: BeforeUnloadEvent) => {
      if (hasUnsavedChanges && !isLeavingRef.current) {
        e.preventDefault();
        e.returnValue = '';
        return '';
      }
    };

    if (hasUnsavedChanges) {
      window.addEventListener('beforeunload', handleBeforeUnload);
    }

    return () => {
      window.removeEventListener('beforeunload', handleBeforeUnload);
    };
  }, [hasUnsavedChanges]);

  // 🔧 React Router 차단 시 모달 표시
  useEffect(() => {
    if (blocker.state === 'blocked') {
      setPendingNavigation(() => () => {
        isLeavingRef.current = true;
        blocker.proceed();
      });
      setShowModal(true);
    }
  }, [blocker]);

  // 🆕 안전한 네비게이션 함수 (프로그래밍적 이동용)
  const safeNavigate = useCallback((navigationFn: () => void) => {
    if (hasUnsavedChanges && !isLeavingRef.current) {
      setPendingNavigation(() => navigationFn);
      setShowModal(true);
    } else {
      navigationFn();
    }
  }, [hasUnsavedChanges]);

  // 🆕 모달 액션 핸들러
  const handleModalConfirm = useCallback(() => {
    setShowModal(false);
    isLeavingRef.current = true;
    
    if (pendingNavigation) {
      pendingNavigation();
      setPendingNavigation(null);
    }
    
    onConfirmLeave?.();
  }, [pendingNavigation, onConfirmLeave]);

  const handleModalCancel = useCallback(() => {
    setShowModal(false);
    setPendingNavigation(null);
    
    // React Router 차단된 네비게이션 리셋
    if (blocker.state === 'blocked') {
      blocker.reset();
    }
  }, [blocker]);

  return {
    showModal,
    safeNavigate,
    handleModalConfirm,
    handleModalCancel
  };
};
```

</details>
- 에디터 화면에서 모달에 연결해서 사용 중

<br />

# 🔗 References
- [React Router v6 - useBlocker](https://reactrouter.com/en/main/hooks/use-blocker)
- [React Router v6 Migration Guide](https://reactrouter.com/en/main/upgrading/v5)
- [ex3](https://velog.io/@sonic/React-Router-useBlocker-%EB%9C%AF%EC%96%B4%EB%B3%B4%EA%B8%B0)
- [ex4](https://velog.io/@cosmos7/%ED%99%94%EB%A9%B4-%EC%9D%B4%ED%83%88-%EB%B0%A9%EC%A7%80-useBlocker)
- [ex5](https://ykss.netlify.app/devlog/use_blocker/)
- [ex6](https://dean83.tistory.com/239)
