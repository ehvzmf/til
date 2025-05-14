> 📅 Date: 2025-05-14

# 📌 Focus  
- 커서 기반 API (Cursor-based Pagination)

<br />

# 📝 Learnings

## ✅ 커서 기반 API

> 데이터를 페이지 단위로 나누어 가져올 때, **"현재 위치(커서)"를 기준으로 다음 데이터를 요청**하는 방식의 API 설계 패턴

- **커서(cursor)**는 마지막 항목의 고유 식별자(예: ID, timestamp)
- 페이지 번호가 아닌 **"다음 기준점"**을 전달하여 데이터를 이어서 가져옴

<br />

## ✅ 구조 예시

### 요청 형식

```http
GET /posts?cursor=1689&limit=10
```

- `cursor`: 현재 마지막 데이터의 ID (기준점)
- `limit`: 가져올 개수

### 응답 예시

```json
{
  "results": [...],
  "nextCursor": 1699,
  "hasMore": true
}
```

- `results`: 현재 응답 데이터 목록
- `nextCursor`: 다음 요청에 사용할 커서
- `hasMore`: 다음 페이지 유무

<br />

## ✅ 페이지네이션 방식 비교

| 항목 | Offset 기반 | Cursor 기반 |
|------|-------------|-------------|
| 방식 | page=2 or offset=20 | cursor=1689 |
| 장점 | 구현 단순, 숫자만 사용 | 성능 좋고, 중복/누락 적음 |
| 단점 | 중복/누락 가능, 느림 | 커서 처리 로직 필요 |
| 성능 | 느림 (OFFSET n은 무거움) | 빠름 (WHERE id < cursor) |

<br />

## ✅ 왜 커서 기반을 사용하는가?

| 이유 | 설명 |
|------|------|
| 데이터 변경에도 안정적 | 중간에 삽입/삭제돼도 중복/누락 방지 |
| 성능 우수 | `OFFSET`보다 `WHERE id < cursor`가 빠름 |
| 무한스크롤 최적화 | `useInfiniteQuery`와 같은 훅에서 자연스럽게 동작

<br />

## ✅ 실무 예: 게시글 무한 로딩

```ts
GET /api/posts?cursor=1024&limit=20
```

```ts
{
  results: [...],
  nextCursor: 1044,
  hasMore: true
}
```

→ 클라이언트에서는 `nextCursor`를 `pageParam`으로 넘겨 다음 요청을 구성

```tsx
getNextPageParam: (lastPage) => lastPage.nextCursor ?? undefined
```

<br />

## ✅ 요약

| 항목 | 내용 |
|------|------|
| 핵심 개념 | 이전 응답의 마지막 항목을 기준으로 다음 데이터를 요청 |
| 장점 | 빠르고, 안정적이며 무한 스크롤에 적합 |
| 사용 예 | 게시글 리스트, 알림 목록, 메시지 채팅 등 |
| 실무 팁 | 정렬 기준(시간, ID 등)에 따라 커서 선택 설계 필요 |

<br />

# 🔗 References

- [Apollo Docs - Cursor-based Pagination](https://www.apollographql.com/docs/react/pagination/cursor-based/)
- [GraphQL Relay Cursor Connection Specification](https://relay.dev/graphql/connections.htm)
