> 📅 Date: 2025-05-21

# 📌 Focus

> **useMutation** <br />
> React Query에서 비동기 쓰기 작업(POST/PUT/DELETE 등)을 처리하는 핵심 훅

<br />

# 📝 Learnings
`useMutation`은 **POST / PUT / PATCH / DELETE 등 데이터를 변경하는 요청**을 처리하는 React Query의 훅
* `useQuery`: read
* `useMutation`: write => 데이터 읽어오는 작업은 모두 이 훅 사용
<br />

### 🔁 useQuery vs useMutation

| 항목            | `useQuery`                | `useMutation`                                      |
| ------------- | ------------------------- | -------------------------------------------------- |
| **용도**        | 데이터를 "읽어오는" 작업 (GET)      | 데이터를 "변경하는" 작업 (POST, PUT, DELETE)                 |
| **자동 호출**     | 컴포넌트 mount 시 자동 호출됨       | 직접 `mutate()`를 호출해야 실행됨                            |
| **캐싱**        | 쿼리 키 기준으로 자동 캐싱           | 기본적으로 캐시하지 않음                                      |
| **성공/실패 핸들링** | `onSuccess`, `onError` 가능 | `onSuccess`, `onError` 등 mutation life cycle 사용 가능 |
| **예시**        | 유저 정보 조회, 투표 여부 확인 등      | 투표 제출, 투표 취소 등                                     |

<br />

## ✅ 기본 사용법

```tsx
const mutation = useMutation({
  mutationFn: (newTodo) =>
    axios.post('/todos', newTodo),
});
```

사용:

```tsx
mutation.mutate({ title: '할 일 추가' });
```

또는:

```tsx
onClick={() => mutation.mutate({ title: '새 항목' })}
```

<br />

## ✅ 반환값 구조

```ts
const {
  mutate,
  mutateAsync,
  isLoading,
  isSuccess,
  isError,
  error,
  data,
  reset,
} = useMutation({...});
```

| 속성              | 설명                        |
| --------------- | ------------------------- |
| `mutate()`      | 비동기 요청 실행 (콜백 기반)         |
| `mutateAsync()` | `await` 가능한 Promise 기반 요청 |
| `isLoading`     | 요청 중 여부                   |
| `isSuccess`     | 성공 여부                     |
| `isError`       | 에러 발생 여부                  |
| `error`         | 에러 객체                     |
| `data`          | 응답 데이터                    |
| `reset()`       | 상태 초기화                    |

<br />

## ✅ 실제 사용 

```tsx
// useDeleteVote.ts
export const useDeleteVote = () => {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: deleteVote,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['check-vote'] });
    },
  });
};

// Component
const deleteMutation = useDeleteVote();

const handleCancelVote = () => {
  deleteMutation.mutate(undefined, {
    onSuccess: () => {
      refetchCheckVote();
      setPick(null);
    },
    onError: () => alert('투표 취소에 실패했습니다.'),
  });
};
```

<br />

## ✅ `mutate` vs `mutateAsync`

| 구분              | 특징                                   |
| --------------- | ------------------------------------ |
| `mutate()`      | 콜백 기반 (`onSuccess`, `onError` 내부 처리) |
| `mutateAsync()` | `await`로 동기처럼 사용 가능                  |

```tsx
const handleVote = async () => {
  try {
    const res = await voteMutation.mutateAsync('abc123');
    console.log('응답:', res);
  } catch (err) {
    alert('에러 발생!');
  }
};
```

<br />

## ✅ 옵션 모음

| 옵션명                      | 설명                          |
| ------------------------ | --------------------------- |
| `mutationFn`             | 실제 실행할 요청 함수 (필수)           |
| `onSuccess(data)`        | 성공 시 실행                     |
| `onError(error)`         | 에러 발생 시 실행                  |
| `onSettled(data, error)` | 성공/실패 상관없이 항상 실행            |
| `onMutate(변경 전 데이터)`     | Optimistic Update에 사용 (롤백용) |

---

## ✅ Usage

| 상황           | 팁                                             |
| ------------ | --------------------------------------------- |
| 성공 후 데이터 최신화 | `queryClient.invalidateQueries()`로 관련 캐시 리프레시 |
| 로딩 UI 필요     | `isLoading` 사용해서 버튼 disabled 또는 spinner 처리    |
| 낙관적 업데이트     | `onMutate` + `context` + `rollback` 활용        |
| 연속 요청 방지     | `isLoading` 중복 호출 방지 로직 필요                    |
