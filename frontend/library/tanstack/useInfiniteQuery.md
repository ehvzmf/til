> 📅 Date: 2025-05-14 <br />
> 📅 Date: 2025-05-19 (하단 요소 감지 추가)

# 📌 Focus  
**무한 스크롤**: 사용자가 페이지 하단에 도달하면 자동으로 다음 데이터를 로딩하는 UI 패턴
일반적은 페이지네이션과 달리 버튼 클릭 없이 스크롤으로 연속적인 데이터를 확인할 수 있음. 

<br />

`useInfiniteQuery`: 무한 스크롤 데이터를 처리하는 React 훅

<br />

# 📝 Learnings

## 🏗️ Structure
- `useInfiniteQuery`
- `IntersectionObserver` (하단 요소 감지) 
- `getNextPageParam`, `fetchNextPage`

<br />

## ✅ useInfiniteQuery

> `useInfiniteQuery`는 **무한 스크롤 형태의 API를 처리할 때 사용하는 React 훅**으로,  
> 페이지 번호 또는 커서(cursor)를 기반으로 데이터를 점진적으로 불러오는 데 최적화되어 있음.

- TanStack Query (React Query)의 기능 중 하나
- `pageParam`을 통해 이전 요청 이후의 **다음 데이터를 로딩**
- 자동 캐싱, 로딩 상태, 에러 처리, refetch 등 포함

<br />

## ✅ 기본 구조

```tsx
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
  status,
  error,
} = useInfiniteQuery({
  queryKey: ['users'],
  queryFn: ({ pageParam = 1 }) =>
    fetchUsers({ page: pageParam }),
  getNextPageParam: (lastPage, allPages) => {
    return lastPage.nextPage ?? false;
  },
});
```

<br />

## ✅ 주요 옵션 설명

| 옵션 | 설명 |
|------|------|
| `queryKey` | 쿼리 식별 키. 캐싱에 사용 |
| `queryFn` | 실제 데이터를 불러오는 함수 |
| `pageParam` | 쿼리 함수가 받는 인자. 현재 페이지 정보 (기본값 지정 필요) |
| `getNextPageParam` | 다음 요청에 넘길 pageParam 반환하는 함수 |
| `getPreviousPageParam` | (선택) 이전 페이지 param 반환. 양방향 페이징 시 사용 |
| `initialPageParam` | 초기 pageParam 값. 기본은 `undefined` |

<br />

## ✅ 반환 객체 설명

| 반환 값 | 설명 |
|---------|------|
| `data` | { pages, pageParams } 형태. 누적된 모든 페이지 데이터 포함 |
| `fetchNextPage()` | 다음 페이지를 가져오는 함수 |
| `hasNextPage` | 다음 페이지가 존재하는지 여부 |
| `isFetchingNextPage` | 현재 다음 페이지를 가져오는 중인지 여부 |
| `status` | loading / error / success 상태 값 |
| `error` | 에러 객체 |

<br />

## ✅ API 응답 예시 전제

서버 응답 형태 예시:

```json
{
  "results": [...],
  "nextPage": 2,
  "hasMore": true
}
```

→ `queryFn`과 `getNextPageParam`을 이 구조에 맞춰 작성해야 함

<br />

## ✅ 실제 예제 코드

```tsx
import { useInfiniteQuery } from '@tanstack/react-query';
import axios from 'axios';

const fetchPosts = async ({ pageParam = 1 }) => {
  const res = await axios.get(`/api/posts?page=${pageParam}`);
  return res.data;
};

const useInfinitePosts = () =>
  useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
    initialPageParam: 1,
    getNextPageParam: (lastPage) =>
      lastPage.hasMore ? lastPage.nextPage : undefined,
  });
```

컴포넌트에서 사용:

```tsx
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfinitePosts();

return (
  <>
    {data?.pages.map((page, i) => (
      <div key={i}>
        {page.results.map(post => (
          <div key={post.id}>{post.title}</div>
        ))}
      </div>
    ))}

    {hasNextPage && (
      <button onClick={() => fetchNextPage()} disabled={isFetchingNextPage}>
        {isFetchingNextPage ? 'Loading...' : 'Load More'}
      </button>
    )}
  </>
);
```

<br />

## ✅ 스크롤로 자동 로딩하기 (IntersectionObserver 사용 예)

```tsx
const bottomRef = useRef(null);

useEffect(() => {
  if (!hasNextPage || isFetchingNextPage) return;

  const observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) {
      fetchNextPage();
    }
  });

  if (bottomRef.current) {
    observer.observe(bottomRef.current);
  }

  return () => observer.disconnect();
}, [hasNextPage, isFetchingNextPage, fetchNextPage]);

return <div ref={bottomRef}></div>;
```

<br />

## ✅ 주의할 점

- **pageParam 기본값 설정 필수**: queryFn에서 `pageParam = 1` 같은 기본값을 설정하지 않으면 undefined로 시작
- **getNextPageParam 로직 주의**: 서버 응답에 따라 nextPage 값을 올바르게 반환해야 함
- **initialPageParam이 생략되면 undefined로 시작**
- **queryKey 충돌 방지**: filter 조건 등 붙일 때 key를 배열로 명확히 구분

<br />

## ✅ 요약

| 항목 | 설명 |
|------|------|
| 역할 | 무한 스크롤 / 페이지네이션 요청 처리 |
| 핵심 요소 | `queryFn`, `pageParam`, `getNextPageParam` |
| 반환값 활용 | `data.pages`, `fetchNextPage`, `hasNextPage` |
| 실무 팁 | IntersectionObserver 활용 시 UX 향상 |

<br />

# 🔗 References

- [TanStack Docs – useInfiniteQuery](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery)
- [React Query Patterns – Infinite Scroll](https://tkdodo.eu/blog/infinite-loading-with-react-query)

---

이 내용을 정리해두면 **무한 스크롤, 커서 기반 API, 리스트 렌더링** 등에서 TanStack Query를 활용할 수 있습니다.  
더 고급 패턴(스크롤 위치 보존, refetchOnWindowFocus 제어 등)도 필요하시면 이어서 정리해드릴게요! 😊
