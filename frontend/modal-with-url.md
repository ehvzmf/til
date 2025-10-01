> 📅 Date: 2025-10-01


# 📌 Focus
- (예: JavaScript, React, Docker 등)

<br />

# 📝 Learnings


### `hooks/useModalWithUrl`

```ts
import { useState, useEffect } from 'react';
import { useSearchParams } from 'react-router-dom';

interface UseModalWithUrlOptions {
  paramName: string; // URL 파라미터 이름 (예: 'politician_id')
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

<br />

# 🔗 References
- [관련 링크](URL)
