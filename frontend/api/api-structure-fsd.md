> 📅 Date: 2025-04-17

# 📌 Focus
- API 호출 구조 설계 (axios) with FSD
- 현재 프로젝트에서 api 사용하는 방식 아카이브

<br />

# 📝 Learnings
> **Overview**
> - /shared/api/에서 `axiosInstance` 정의 후 api 호출할 때마다 사용
> - /shared/types/ 혹은 각 entities/features에서 request/response interface type 관리
> - `/entities/api/` || `/features/api/` 에서 api 요청 (get~, fetch~) 
> - `/entities/model/` || `/features/model/`에서 불러온 데이터 사용 (use~)
> - > 실제 컴포넌트에서는 이 부분 사용

### 📦 FSD 구조에서 API 호출 구성

#### 1. `shared/api/axiosInstance.ts`
```ts
import axios from 'axios';

export const axiosInstance = axios.create({
  baseURL: '/api',
  timeout: 5000,             // 요청 제한 시간 (ms)
  withCredentials: false,    // 쿠키 포함 여부
  headers: {
    'Content-Type': 'application/json',
    Accept: 'application/json',
  },
});
```
> ✅ **추가 옵션 추천**
> - `baseURL`: 공통 URL Prefix 지정
> - `timeout`: 요청 제한 시간 설정 (예: 5000ms)
> - `headers`: 기본 헤더 (JSON 응답 설정)
> - `paramsSerializer`: 쿼리 파라미터 커스텀 처리
> - `interceptors`: 요청/응답 가로채기 (예: 에러 핸들링, 토큰 자동 추가)

---

#### 2. FSD 구조에서 API 분리 방식

```bash
features/
└── search/
    ├── api/       # fetch 함수
    │   └── fetchResults.ts
    ├── model/     # 타입 정의, 로직, 훅
    │   ├── searchTypes.ts
    │   └── useSearch.ts
```

```ts
// api/fetchResults.ts
import { axiosInstance } from '@/shared/api/axiosInstance';
import { SearchResponse } from '../model/searchTypes';

export const fetchResults = async (query: string): Promise<SearchResponse> => {
  const res = await axiosInstance.get(`/search/?query=${encodeURIComponent(query)}`);
  return res.data;
};
```

```ts
// model/useSearch.ts
import { useState } from 'react';
import { fetchResults } from '../api/fetchResults';
import { SearchResponse } from './searchTypes';

export const useSearch = () => {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<SearchResponse['search_results']>([]);
  const [isLoading, setIsLoading] = useState(false);

  const search = async (q: string) => {
    if (q.length < 2) return;
    setIsLoading(true);
    const res = await fetchResults(q);
    setResults(res.search_results);
    setIsLoading(false);
  };

  return { query, setQuery, results, isLoading, search };
};
```

> 💡 `SearchResponse`와 같은 타입은 model에서 관리

---

### 📌 `entities` vs `features`

| 항목 | `entities` | `features` |
|------|------------|------------|
| 의미 | 핵심 도메인 단위 | 사용자 행동 단위 |
| 예시 | User, Article, Product | 로그인, 검색, 필터링 |
| 포함 요소 | 모델, 스토어, API 호출, UI 조각 | 여러 entities를 조합한 유즈케이스 |
| 사용 위치 | 페이지 및 features에서 공유 | 주로 UI interaction과 결합됨 |
<br />
-> 도메인과 기능으로 구분

---

### 🧩 실제 컴포넌트 사용 예

```tsx
// pages/search/SearchPage.tsx
import { useSearch } from '@/features/search/model/useSearch';

export const SearchPage = () => {
  const { query, setQuery, search, results, isLoading } = useSearch();

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        onKeyDown={(e) => e.key === 'Enter' && search(query)}
      />
      {isLoading ? (
        <p>Loading...</p>
      ) : (
        results.map((item) => <div key={item.id}>{item.name}</div>)
      )}
    </div>
  );
};
```

---

# 🔗 References
- [Axios Docs](https://axios-http.com/)
- [Tanstack Query Docs](https://tanstack.com/query/v4)
- [Feature-Sliced Design](https://feature-sliced.design/)
