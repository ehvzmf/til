> 📅 Date: 2025-05-21

# 📌 Focus

> 1. **Optimistic Update 패턴**
> 2. **Mutation 중복 방지 전략**
> 3. **Form 제출과의 통합**

<br /> 

# 📝 Learnings

## ✅ 1. Optimistic Update 패턴

> "서버 응답을 기다리지 않고 먼저 UI를 변경하고, 나중에 실패하면 롤백하는" UX 중심 전략

### 💡 사용 목적

* 빠른 반응성 → 사용자 만족도 ↑
* 서버-클라이언트 간 지연 보정
* 채팅, 좋아요, 투표, 팔로우 등 즉시 반영이 필요한 UI에 필수

<br />

### 🧠 기본 흐름

1. `onMutate` → 변경 전 상태 백업 + UI 업데이트
2. 요청 성공 → 캐시 무효화 or 유지
3. 요청 실패 → `context`에 있던 이전 상태로 롤백

<br />

### 📦 예제: 좋아요 버튼

```tsx
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: (postId: string) =>
    axios.post(`/api/posts/${postId}/like`),

  // 1️⃣ Optimistic update
  onMutate: async (postId) => {
    await queryClient.cancelQueries(['post-list']);

    const previousPosts = queryClient.getQueryData<Post[]>('post-list');

    queryClient.setQueryData<Post[]>('post-list', (old) =>
      old?.map((post) =>
        post.id === postId ? { ...post, likes: post.likes + 1 } : post
      )
    );

    return { previousPosts };
  },

  // 2️⃣ 롤백
  onError: (err, _, context) => {
    if (context?.previousPosts) {
      queryClient.setQueryData('post-list', context.previousPosts);
    }
  },

  // 3️⃣ 성공 후 최신화
  onSettled: () => {
    queryClient.invalidateQueries(['post-list']);
  },
});
```

<br />

### ✅ 실무 팁

| 항목       | 설명                                     |
| -------- | -------------------------------------- |
| 롤백 여부 확인 | `onError`에서 context 유무 확인              |
| 중복 요청 방지 | `cancelQueries()`로 기존 요청 중단            |
| 정확도 ↑    | `invalidateQueries()`로 최신 서버 데이터 fetch |

<br />

---

<br />

## ✅ 2. Mutation 중복 방지 전략

> **사용자가 버튼을 여러 번 클릭해 중복 요청이 발생하는 것**을 방지하는 실무 전략

<br />

### 🚫 왜 위험한가?

* 서버에 동일 요청이 여러 번 전송
* DB 중복 반영, race condition 유발 가능
* UI 상태 오염

<br />

### ✅ 전략 1: `isLoading` 체크

```tsx
<Button
  disabled={mutation.isLoading}
  onClick={() => mutation.mutate(data)}
>
  제출하기
</Button>
```

* 가장 기본적이고 직관적인 방식

<br />

### ✅ 전략 2: `useRef` + manual lock

```tsx
const isLocked = useRef(false);

const handleClick = () => {
  if (isLocked.current) return;

  isLocked.current = true;

  mutation.mutate(data, {
    onSettled: () => {
      isLocked.current = false;
    },
  });
};
```

* 반복 클릭 막고 요청 완료 후 잠금 해제
* debounce와 함께 사용 가능

<br />

### ✅ 전략 3: `mutation.isSuccess` 이후 다시 허용

```tsx
useEffect(() => {
  if (mutation.isSuccess) {
    // 버튼 재활성화 or form 초기화
  }
}, [mutation.isSuccess]);
```

<br />

---

<br />

## ✅ 3. Form 제출과의 통합

> `react-hook-form` 또는 `formik` 같은 폼 라이브러리와 `useMutation`을 통합하여
> **비동기 제출, 에러 핸들링, 로딩 처리까지 일관된 방식으로 처리**

<br />

### 🧩 예제 (react-hook-form)

```tsx
import { useForm } from 'react-hook-form';

const { register, handleSubmit, formState, reset } = useForm();

const mutation = useMutation({
  mutationFn: (formData) => axios.post('/api/posts', formData),
  onSuccess: () => {
    reset(); // 입력 초기화
    alert('등록 성공!');
  },
  onError: () => {
    alert('에러 발생');
  },
});

const onSubmit = (data) => {
  mutation.mutate(data);
};
```

```tsx
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register('title')} />
  <textarea {...register('content')} />
  <button disabled={mutation.isLoading}>등록</button>
</form>
```

<br />

### 💡 Form 연동 전략 요약

| 항목     | 설명                                                |
| ------ | ------------------------------------------------- |
| 비동기 요청 | `useMutation`으로 통합                                |
| 상태 관리  | `formState.isSubmitting`, `mutation.isLoading` 활용 |
| 초기화    | `reset()` 사용                                      |
| 에러 표시  | `mutation.error`, `formState.errors` 함께 활용        |

<br />

## ✨ 요약 정리

| 주제                | 설명                     | 핵심 기술                                                |
| ----------------- | ---------------------- | ---------------------------------------------------- |
| Optimistic Update | 응답 전에 UI 선반영 → 실패 시 롤백 | `onMutate`, `context`, `onError`                     |
| 중복 방지             | 빠른 클릭으로 다중 요청 차단       | `isLoading`, `useRef`, `debounce`                    |
| Form 연동           | form 요청을 mutation으로 처리 | `react-hook-form`, `handleSubmit`, `mutation.mutate` |
