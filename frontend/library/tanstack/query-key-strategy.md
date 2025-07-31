> 📅 Date: 2025-07-31

# 📌 Focus
- TanStack Query: Query Key 설계 전략
- 쿼리 키 구조화, 동적 파라미터, 중첩 키, 캐싱 전략
<br />

# 📝 Learnings

## Query Key
- TanStack Query(React Query)에서 쿼리의 고유성을 결정하는 핵심 요소
- 쿼리 키가 다르면 서로 다른 데이터로 인식되어 별도의 캐시 공간에 저장
- 쿼리 키가 같으면 동일한 데이터로 간주되어 캐시 공유
- 쿼리 키 설계는 데이터 일관성, 캐싱 효율, 성능 최적화에 직접적인 영향

<br />

## Query Key의 기본 구조와 설계 원칙

### 배열 기반 구조
- 문자열이 아닌 배열로 설계하는 것이 권장된다.
- 배열의 각 요소는 쿼리의 계층적 의미 표현
<br />

```js
// 단순 문자열 (비추천)
useQuery('user', fetchUser)

// 배열 기반 (권장)
useQuery(['user', userId], fetchUser)
```

- 첫 번째 요소: 리소스의 타입(예: 'user', 'posts')
- 두 번째 이후 요소: 식별자, 파라미터, 필터 등
<br />

### 명확하고 일관된 네이밍
- 리소스의 종류, 목적, 파라미터를 명확히 구분
- 중첩 구조를 활용해 계층적 의미를 부여
- 예시: `['posts', 'list', { page: 1, filter: 'hot' }]`

<br />

## 동적 파라미터와 중첩 키 설계

### 동적 파라미터 활용
API 요청에 따라 동적으로 변하는 값(예: userId, page, filter 등)은 쿼리 키의 두 번째 이후 요소로 명확히 분리
<br />

```js
useQuery(['user', userId], fetchUser)
useQuery(['posts', { page, filter }], fetchPosts)
```

- 객체를 파라미터로 쿼리 키에 포함할 때는 순서와 속성명이 일관되게 유지되어야 함
- 불필요한 값(예: undefined, null)은 포함하지 않는 것이 좋음

### 중첩 키 설계
복잡한 데이터 구조나 계층적 리소스(예: 프로젝트 > 게시글 > 댓글)에서는 중첩 배열로 설계
<br />

```js
useQuery(['projects', projectId, 'posts', postId, 'comments'], fetchComments)
```

- 계층 구조를 명확히 표현하여, 부분 무효화(invalidate)나 refetch 시 유연하게 대응 가능

<br />

## 캐싱 전략과 쿼리 키의 관계

### 캐시 분리와 공유
- 쿼리 키가 다르면 별도의 캐시 공간에 저장됨 → 데이터 충돌 방지
- 쿼리 키가 같으면 동일한 캐시를 공유 → 불필요한 네트워크 요청 방지

### Query Invalidation(무효화)와 Refetch
- 쿼리 키를 계층적으로 설계하면, 상위 리소스 변경 시 하위 리소스만 부분적으로 무효화 가능
- 예시: `queryClient.invalidateQueries(['projects', projectId])` → 해당 프로젝트의 모든 하위 데이터 무효화

### StaleTime, CacheTime과의 연계
- 쿼리 키별로 staleTime, cacheTime을 다르게 설정해 데이터 신선도와 메모리 사용량을 조절

<br />

## 실무 적용 시 고려사항

### 일관성 있는 쿼리 키 네이밍 컨벤션
- 팀 내에서 쿼리 키 네이밍 규칙을 문서화하고, 모든 쿼리에 동일하게 적용
- 예시: `[리소스, 식별자, 세부타입, 파라미터]` 순서로 통일

### 파라미터 객체의 직렬화 주의
- 객체를 쿼리 키에 포함할 때는 순서, 속성명, 값이 항상 동일하게 생성되도록 주의
- 불필요한 속성, undefined/null 값은 제거
- JSON.stringify를 직접 사용하지 말고, TanStack Query 내부 직렬화 방식을 신뢰

### 불필요한 쿼리 키 분할/중복 방지
- 동일한 데이터를 여러 키로 쪼개서 관리하면 캐시가 분산되어 비효율적
- 반대로, 너무 포괄적인 키는 불필요한 refetch/무효화로 이어질 수 있음

### invalidateQueries/ refetchQueries 활용
- 계층적 쿼리 키 설계를 통해, 상위 리소스 변경 시 하위 리소스만 부분적으로 무효화
- 예시: 게시글 수정 시 `['posts', postId]`만 invalidate, 전체 리스트는 그대로 유지

### SSR/Prefetch/Infinite Query와의 연계
- SSR/Prefetch 시에도 동일한 쿼리 키를 사용해야 클라이언트와 캐시가 일치
- Infinite Query에서는 페이지 파라미터를 쿼리 키에 명확히 포함

<br />

## 실전 예제와 패턴

### 기본 패턴
```js
// 단일 리소스
useQuery(['user', userId], fetchUser)

// 리스트 + 파라미터
useQuery(['posts', { page, filter }], fetchPosts)

// 중첩 리소스
useQuery(['projects', projectId, 'posts', postId, 'comments'], fetchComments)
```

### 부분 무효화
```js
// 특정 프로젝트의 모든 데이터 무효화
queryClient.invalidateQueries(['projects', projectId])

// 특정 게시글만 무효화
queryClient.invalidateQueries(['posts', postId])
```

### 동적 파라미터 객체 관리
```js
// 파라미터 객체를 쿼리 키에 포함할 때, undefined/null 제거
const queryKey = ['posts', { page: page || 1, filter: filter || 'all' }]
useQuery(queryKey, fetchPosts)
```

### Infinite Query
```js
useInfiniteQuery(['posts', { filter }], fetchInfinitePosts, {
  getNextPageParam: lastPage => lastPage.nextCursor,
})
```

---

## 여러 고민들 

### "왜 invalidateQueries가 안 먹히지?"
- 쿼리 키에 포함된 객체의 속성 순서, 값이 다르면 다른 쿼리로 인식됨
- 항상 동일한 순서와 값으로 쿼리 키를 생성해야 함

### "동일한 데이터인데 캐시가 분산된다"
- 파라미터가 다르게 들어가거나, 불필요한 값이 포함되면 캐시가 분산됨
- 쿼리 키 생성 로직을 util 함수로 통일하는 것도 방법

### "리스트와 상세 쿼리의 키를 어떻게 나눌까?"
- 리스트: `['posts', { page, filter }]`
- 상세: `['posts', postId]`
- 리스트 무효화 시 상세는 유지, 상세 무효화 시 리스트는 영향 없음

### "중첩 리소스에서 invalidate 범위 제어"
- 계층적으로 설계하면 상위/하위 리소스만 부분적으로 무효화 가능
- 예: `['projects', projectId, 'posts']` → 해당 프로젝트의 게시글만 무효화

### "SSR/Prefetch와 쿼리 키 일치"
- 서버와 클라이언트 모두 동일한 쿼리 키 생성 로직을 사용해야 캐시가 일치
- 쿼리 키 생성 유틸을 별도 파일로 분리해 공유

<br />

# 🔗 References
- [TanStack Query 공식 문서: Query Keys](https://tanstack.com/query/latest/docs/framework/react/guides/query-keys)
- [Query Invalidation & Refetching](https://tanstack.com/query/latest/docs/framework/react/guides/query-invalidation)
- [React Query Patterns: Query Key Design](https://tkdodo.eu/blog/effective-react-query-keys)
- [TanStack Query GitHub Discussions](https://github.com/TanStack/query/discussions)
