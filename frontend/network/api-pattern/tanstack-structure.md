> 📅 Date: 2025-04-17

# 📌 Focus
- FSD 구조에서 tanstack query를 활용한 API 데이터 패칭 방식

<br />

# 📝 Learnings

### 📦 Why tanstack query?
- `useState`와 직접 fetch 방식은 단순하지만,
  - 로딩/에러 상태 수동 처리
  - 데이터 캐싱 미지원
  - refetch, polling, pagination 등 기능 부족
- tanstack query는 **자동 캐싱, 상태관리, 비동기 흐름 관리**를 담당

---

### 🧩 FSD 구조에서의 적용

```bash
features/
└── search/
    ├── api/       # fetch 함수
    │   └── fetchResults.ts
    ├── model/
    │   ├── searchTypes.ts
    │   └── useSearchQuery.ts
```

---

### 🧪 API 호출 함수 (기존 axios 재사용)

```ts
// api/fetchResults.ts
import { axiosInstance } from '@/shared/api/axiosInstance';
import { SearchResponse } from '../model/searchTypes';

export const fetchResults = async (query: string): Promise<SearchResponse> => {
  const res = await axiosInstance.get(`/search/?query=${encodeURIComponent(query)}`);
  return res.data;
};
```

---

### 🔍 tanstack query 훅 정의

```ts
// model/useSearchQuery.ts
import { useQuery } from '@tanstack/react-query';
import { fetchResults } from '../api/fetchResults';

export const useSearchQuery = (query: string) => {
  return useQuery({
    queryKey: ['search', query],
    queryFn: () => fetchResults(query),
    enabled: query.length >= 2, // 빈 문자열 방지
    staleTime: 1000 * 60 * 5,   // 5분간 캐싱
  });
};
```

---

### 💡 타입 주의 사항
- `fetchResults` 반환 타입은 반드시 명시 (`Promise<SearchResponse>`)
- 쿼리 훅에서 사용하는 `queryKey`는 **고유하고 예측 가능**해야 함

---

### 🧩 컴포넌트에서 사용

```tsx
// pages/search/SearchPage.tsx
import { useState } from 'react';
import { useSearchQuery } from '@/features/search/model/useSearchQuery';

export const SearchPage = () => {
  const [query, setQuery] = useState('');
  const { data, isLoading, isError } = useSearchQuery(query);

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="정치인 이름 입력"
      />
      {isLoading && <p>검색 중...</p>}
      {isError && <p>에러가 발생했습니다.</p>}
      {data?.search_results.map((item) => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  );
};
```

---

### ✅ 정리
- `tanstack query`는 API 호출 시 발생하는 반복적인 상태 관리 로직을 줄여줌
- FSD 구조 내에서도 `model/`에 로직을 넣어 깔끔하게 정리 가능
- 서버 상태는 query, 클라이언트 상태는 useState → 역할 분리

---

# 🔗 References
- [Tanstack Query](https://tanstack.com/query/v4/docs)
- [Feature-Sliced Design](https://feature-sliced.design/)
