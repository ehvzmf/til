> 📅 Date: 2025-05-21

# 📌 Focus

>   useMutation 활용 전략
> 1. `cancelMutation()`
> 2. `mutationKey`
> 3. Error Boundary 연동

<br />

# 📝 Learnings

## `cancelMutation()`
> 아직 **완료되지 않은 Mutation을 취소**할 수 있는 함수
> → 서버 요청이 완료되기 전에 취소하거나, 이전 요청을 무효화할 때 사용

<br />

| 상황                 | 설명                                  |
| ------------------ | ----------------------------------- |
| 1️⃣ 빠른 사용자 입력      | 사용자가 연속으로 버튼을 눌러 여러 요청을 보내는 경우      |
| 2️⃣ 화면 전환          | 페이지 이동 중 불필요한 요청이 남아 있는 경우          |
| 3️⃣ race condition | 이전 요청보다 나중 요청이 먼저 도착하면 UI 꼬임 발생 가능성 |

<br />

### 🧩 예시: 실시간 닉네임 중복 확인

```tsx
const mutation = useMutation({
  mutationFn: (nickname: string) =>
    axios.get(`/api/check-nickname?value=${nickname}`),
});

const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  mutation.cancel(); // 이전 요청 취소
  mutation.mutate(e.target.value); // 최신 값으로 요청
};
```

✅ 입력값이 바뀔 때마다 요청은 생기지만, **이전 요청은 취소되므로** 
서버에 불필요한 부하가 줄어들고, 최신 상태만 UI에 반영된다. 

<br />

---

<br />

## `mutationKey`

> `mutationKey`는 특정 Mutation을 **고유하게 식별**할 수 있도록 해주는 키값입니다.

<br />

```tsx
useMutation({
  mutationKey: ['vote', politicianId],
  ...
});
```

<br />

| 용도     | 설명                              |
| ------ | ------------------------------- |
| 요청 추적  | 동일한 mutation 중 하나만 추적 가능        |
| 개발자 도구 | Devtools에서 mutation 단위 확인       |
| 상태 구분  | `useMutationState()` 등에서 필터링 가능 |
| 자동 캐시  | 동일한 mutation 재사용 가능             |

<br />

### 🧩 예시: 여러 게시글 좋아요 처리

```tsx
const likeMutation = useMutation({
  mutationKey: ['like', postId],
  mutationFn: () => axios.post(`/api/posts/${postId}/like`),
});
```

→ 각 게시글 별로 고유한 mutation 상태 추적 가능

<br />

### 🛠️ 활용: useMutationState()

```tsx
const allVotingMutations = useMutationState({
  filters: {
    mutationKey: ['vote'],
    status: 'pending',
  },
});
```

→ 현재 진행 중인 "vote" 관련 요청들만 추출 가능

<br />

---

<br />

## Error Boundary 연동
> React Query는 에러가 발생하면 `error boundary`에 전달할 수 있음.
> 이때 `useMutation`도 이를 연동해서 처리 가능

<br />

| 상황        | 설명                                |
| --------- | --------------------------------- |
| 예외 처리     | 치명적인 요청 실패 시 사용자에게 fallback UI 제공 |
| 전체 앱 보호   | 네트워크 문제, 서버 장애 등 예상치 못한 오류 대응     |
| 사용자 경험 개선 | 무조건 alert 대신 UI 기반 에러 처리 가능       |

<br />

### 🛠️ 구성 요소

1. `useMutation({ throwOnError: true })`
2. React `ErrorBoundary` 컴포넌트

<br />

### 📦 예시 코드

#### 1. 에러 바운더리 설정

```tsx
class MyErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h2>문제가 발생했습니다. 다시 시도해주세요.</h2>;
    }
    return this.props.children;
  }
}
```

#### 2. useMutation에서 오류 throw

```tsx
const mutation = useMutation({
  mutationFn: () => axios.post('/api/fail'), 
  throwOnError: true,
});
```

#### 3. App에 적용

```tsx
<MyErrorBoundary>
  <MyComponentUsingMutation />
</MyErrorBoundary>
```

✅ 이제 mutation 실패 시 자동으로 ErrorBoundary가 잡아주고 fallback UI를 보여줍니다.

<br />

---

<br />

## 🧩 요약 비교

| 항목                 | 설명                | 추천 상황                  |
| ------------------ | ----------------- | ---------------------- |
| `cancelMutation()` | 이전 mutation 취소    | 실시간 입력, 빠른 전환          |
| `mutationKey`      | 고유 식별자 부여         | 대규모 상태 추적, Devtools 활용 |
| Error Boundary     | 오류 UI graceful 처리 | 치명적 에러 대응, 사용자 보호      |

---

## ✅ 실무 적용 전략

| 전략                | 설명                                        |
| ----------------- | ----------------------------------------- |
| 네이밍 규칙 통일         | `mutationKey: ['vote', id]`처럼 일관된 키 설계    |
| Error fallback 개선 | 사용자에게 유의미한 메시지 제공 (예: "네트워크 연결을 확인하세요")   |
| Mutation 추적       | `useMutationState()` 활용해 전체 상태 대시보드 가능    |
| 타이밍 제어            | `cancelMutation()` + debounce 조합으로 UX 최적화 |
