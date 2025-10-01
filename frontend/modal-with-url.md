> 📅 Date: 2025-10-01

# 📌 Focus
- React URL 동기화 패턴
- Custom Hook 설계
- useSearchParams 활용
- 모달 상태 관리

<br />

# 📝 Learnings

## 모달 상태 동기화
> 새로고침 & url 공유를 위해 도입해보자! 

<br />

**웹 애플리케이션에서 모달의 상태를 URL과 동기화하면:**

- **브라우저 뒤로가기/앞으로가기 지원**: 사용자가 브라우저 버튼으로 모달을 닫을 수 있음
- **직접 링크 공유 가능**: 특정 모달이 열린 상태의 URL을 다른 사람과 공유 가능
- **새로고침 시 상태 유지**: 페이지를 새로고침해도 모달 상태가 복원됨
- **SEO 친화적**: 검색 엔진이 모달 내용을 인덱싱할 수 있음

## useModalWithUrl Hook 구현

```ts
import { useState, useEffect } from 'react';
import { useSearchParams } from 'react-router-dom';

interface UseModalWithUrlOptions {
  paramName: string;
}

export const useModalWithUrl = ({ paramName }: UseModalWithUrlOptions) => {
  const [searchParams, setSearchParams] = useSearchParams();
  const [isOpen, setIsOpen] = useState(false);
  const [selectedId, setSelectedId] = useState<string>('');

  // 컴포넌트 마운트 시 URL에서 파라미터 확인
  useEffect(() => {
    const idFromUrl = searchParams.get(paramName);
    if (idFromUrl) {
      setSelectedId(idFromUrl);
      setIsOpen(true);
    }
  }, [searchParams, paramName]);

  // 모달 열기 함수
  const openModal = (id: string) => {
    setSelectedId(id);
    setIsOpen(true);
    // URL에 쿼리 파라미터 추가
    const newSearchParams = new URLSearchParams(searchParams);
    newSearchParams.set(paramName, id);
    setSearchParams(newSearchParams);
  };

  // 모달 닫기 함수
  const closeModal = () => {
    setIsOpen(false);
    setSelectedId('');
    // URL에서 쿼리 파라미터 제거
    const newSearchParams = new URLSearchParams(searchParams);
    newSearchParams.delete(paramName);
    setSearchParams(newSearchParams);
  };

  return {
    isOpen,
    selectedId,
    openModal,
    closeModal,
  };
};
```

## 적용

```tsx
// ProductList 컴포넌트에서 사용
function ProductList() {
  const { isOpen, selectedId, openModal, closeModal } = useModalWithUrl({
    paramName: 'productId'
  });

  return (
    <div>
      {products.map(product => (
        <div key={product.id} onClick={() => openModal(product.id)}>
          {product.name}
        </div>
      ))}
      
      {isOpen && (
        <ProductDetailModal 
          productId={selectedId}
          onClose={closeModal}
        />
      )}
    </div>
  );
}
```

## Hook의 핵심 설계 포인트

### 1. useSearchParams 활용
React Router의 `useSearchParams`를 사용하여 URL 쿼리 파라미터를 안전하게 조작한다.

```ts
// 기존 파라미터 유지하면서 새로운 파라미터 추가
const newSearchParams = new URLSearchParams(searchParams);
newSearchParams.set(paramName, id);
setSearchParams(newSearchParams);
```

### 2. 초기화 로직
컴포넌트 마운트 시 URL에서 파라미터를 읽어 초기 상태를 설정한다.

```ts
useEffect(() => {
  const idFromUrl = searchParams.get(paramName);
  if (idFromUrl) {
    setSelectedId(idFromUrl);
    setIsOpen(true);
  }
}, [searchParams, paramName]);
```

### 3. 상태 동기화
모달 열기/닫기 시 내부 상태와 URL을 동시에 업데이트한다.

## 고급 활용 패턴

### 1. 여러 모달 동시 관리

```ts
export const useMultiModalWithUrl = () => {
  const productModal = useModalWithUrl({ paramName: 'productId' });
  const userModal = useModalWithUrl({ paramName: 'userId' });
  
  return {
    productModal,
    userModal,
  };
};
```

### 2. 모달 히스토리 관리

```ts
export const useModalWithHistory = ({ paramName }: UseModalWithUrlOptions) => {
  const baseHook = useModalWithUrl({ paramName });
  
  const openModalWithHistory = (id: string) => {
    // 현재 상태를 히스토리에 푸시
    window.history.pushState(null, '', `?${paramName}=${id}`);
    baseHook.openModal(id);
  };
  
  return {
    ...baseHook,
    openModal: openModalWithHistory,
  };
};
```

### 3. 타입 안전성 강화

```ts
interface UseModalWithUrlOptions<T = string> {
  paramName: string;
  serialize?: (value: T) => string;
  deserialize?: (value: string) => T;
}

export const useModalWithUrl = <T = string>({
  paramName,
  serialize = (value: T) => String(value),
  deserialize = (value: string) => value as T,
}: UseModalWithUrlOptions<T>) => {
  // 제네릭을 활용한 타입 안전한 구현
};
```

## 실무 적용 시 고려사항

### 1. SEO 최적화
모달 내용이 중요한 경우, 서버사이드 렌더링 시에도 모달이 열린 상태로 렌더링되도록 구현

```ts
// Next.js에서의 SSR 고려
useEffect(() => {
  if (typeof window !== 'undefined') {
    const idFromUrl = searchParams.get(paramName);
    if (idFromUrl) {
      setSelectedId(idFromUrl);
      setIsOpen(true);
    }
  }
}, [searchParams, paramName]);
```

### 2. 접근성 고려
모달이 열릴 때 포커스 관리와 스크린 리더 대응

```ts
const openModal = (id: string) => {
  setSelectedId(id);
  setIsOpen(true);
  
  const newSearchParams = new URLSearchParams(searchParams);
  newSearchParams.set(paramName, id);
  setSearchParams(newSearchParams);
  
  // 접근성: 모달에 포커스 이동
  setTimeout(() => {
    const modal = document.querySelector('[role="dialog"]');
    if (modal) {
      (modal as HTMLElement).focus();
    }
  }, 100);
};
```

### 3. 성능 최적화
불필요한 리렌더링 방지를 위한 메모이제이션

```ts
const memoizedHandlers = useMemo(() => ({
  openModal,
  closeModal,
}), [paramName, searchParams]);
```

### 4. 에러 처리
잘못된 ID나 파라미터에 대한 예외 처리

```ts
useEffect(() => {
  const idFromUrl = searchParams.get(paramName);
  if (idFromUrl) {
    // ID 유효성 검증
    if (isValidId(idFromUrl)) {
      setSelectedId(idFromUrl);
      setIsOpen(true);
    } else {
      // 잘못된 ID인 경우 URL에서 제거
      const newSearchParams = new URLSearchParams(searchParams);
      newSearchParams.delete(paramName);
      setSearchParams(newSearchParams, { replace: true });
    }
  }
}, [searchParams, paramName]);
```

## 관련 패턴과 대안

### 1. React Router의 중첩 라우팅 활용
복잡한 모달의 경우 별도 라우트로 구현하는 것도 고려

```tsx
// App.tsx
<Routes>
  <Route path="/products" element={<ProductList />}>
    <Route path="product/:id" element={<ProductDetailModal />} />
  </Route>
</Routes>
```

### 2. Zustand나 Redux와의 조합
전역 상태 관리와 URL 동기화를 함께 사용

```ts
// Zustand store
interface ModalStore {
  openModals: Set<string>;
  openModal: (id: string) => void;
  closeModal: (id: string) => void;
}

const useModalStore = create<ModalStore>((set) => ({
  openModals: new Set(),
  openModal: (id) => set((state) => ({
    openModals: new Set([...state.openModals, id])
  })),
  closeModal: (id) => set((state) => {
    const newSet = new Set(state.openModals);
    newSet.delete(id);
    return { openModals: newSet };
  }),
}));
```

<br />

# 🔗 References
- [React Router useSearchParams](https://reactrouter.com/en/main/hooks/use-search-params)
- [URLSearchParams MDN](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)
- [Web Accessibility for Modals](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/)
- [Next.js Dynamic Routing](https://nextjs.org/docs/routing/dynamic-routes)