> 📆 2025-08
>



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

https://velog.io/@sonic/React-Router-useBlocker-%EB%9C%AF%EC%96%B4%EB%B3%B4%EA%B8%B0
https://velog.io/@cosmos7/%ED%99%94%EB%A9%B4-%EC%9D%B4%ED%83%88-%EB%B0%A9%EC%A7%80-useBlocker
https://ykss.netlify.app/devlog/use_blocker/
https://dean83.tistory.com/239
