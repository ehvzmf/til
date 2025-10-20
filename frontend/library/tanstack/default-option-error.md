> 📅 Date: 2025-10-19

# 📌 Focus
- TanStack Query v5 DefaultOptions
- TypeScript 타입 호환성 문제
- Mutation Callback 파라미터 불일치
<br />

# 🐛 Issue
> TanStack Query에서 `queryClient.setDefaultOptions()`로 전역 Mutation 콜백을 설정한 후, <br />
> 개별 Mutation에서 이 전역 콜백을 수동으로 호출할 때 TypeScript 타입 에러가 발생 <br />
> (+ 전역 콜백 동작 x)

<br />
**에러 메시지:**
```
Type '(data: unknown, variables: unknown, context: unknown, mutateOptions?: MutateOptions<unknown, unknown, unknown, unknown> | undefined) => unknown' 
is not assignable to type 'MutationOptions<unknown, unknown, unknown, unknown>["onSuccess"]'
```

<br />

# 🔍 Context
- **환경**: TanStack Query v5, TypeScript 5.x
- **발생 조건**: 
  - `queryClient.setDefaultOptions()`로 전역 Mutation 콜백 설정
  - 개별 Mutation에서 전역 콜백을 수동으로 호출하려고 할 때
- **문제 코드**:
```ts
// 전역 설정
queryClient.setDefaultOptions({
  mutations: {
    onSuccess: (data, variables, context) => {
      console.log('전역 성공 처리');
    }
  }
});

// 개별 Mutation에서 전역 콜백 호출 시도 (에러 발생)
useMutation({
  mutationFn: updateUser,
  onSuccess: (data, variables, context) => {
    // 전역 콜백 호출 - 타입 에러 발생!
    queryClient.getDefaultOptions().mutations?.onSuccess?.(data, variables, context);
    // 추가 로직...
    showToast('업데이트 완료');
  }
});
```

<br />

# 💡 Solution

## 해결 방법

### 1. 타입 단언(Type Assertion) 사용 (권장)
```ts
useMutation({
  mutationFn: updateUser,
  onSuccess: (data, variables, context) => {
    // 타입 단언으로 TypeScript 타입 체크 우회
    (queryClient.getDefaultOptions().mutations?.onSuccess as any)?.(data, variables, context);
    
    // 추가 개별 로직
    showToast('업데이트 완료');
    queryClient.invalidateQueries({ queryKey: ['users'] });
  }
});
```

### 2. 명시적 파라미터 전달 (타입 안전)
```ts
useMutation({
  mutationFn: updateUser,
  onSuccess: (data, variables, context, mutateOptions) => {
    // 4번째 파라미터까지 명시적으로 전달
    queryClient.getDefaultOptions().mutations?.onSuccess?.(data, variables, context, mutateOptions);
    
    // 추가 개별 로직
    showToast('업데이트 완료');
  }
});
```

## 문제 원인 분석

### 1. TanStack Query v5 타입 정의
TanStack Query v5에서 `onSuccess` 콜백의 타입 시그니처:
```ts
type MutationOnSuccess<TData, TError, TVariables, TContext> = (
  data: TData,
  variables: TVariables,
  context: TContext,
  mutateOptions?: MutateOptions<TData, TError, TVariables, TContext>
) => unknown
```

### 2. TypeScript 타입 불일치
- **전역 설정**: 3개 파라미터로 정의 (`data`, `variables`, `context`)
- **타입 시스템**: 4개 파라미터 예상 (+ `mutateOptions`)
- **결과**: 타입 호환성 에러 발생

### 3. JavaScript 런타임 동작
JavaScript에서는 함수 호출 시 정의된 파라미터보다 적은 수의 인자를 전달해도 정상 동작:
```js
function example(a, b, c, d) {
  console.log(a, b, c); // d는 undefined
}

example(1, 2, 3); // 정상 동작, d는 undefined
```

## 권장 패턴

### 전역 콜백과 개별 콜백 조합 패턴
```ts
// 1. queryClient 전역 설정
queryClient.setDefaultOptions({
  mutations: {
    onSuccess: (data, variables, context) => {
      // 공통 성공 처리 로직
      console.log('Mutation 성공:', data);
    },
    onError: (error, variables, context) => {
      // 공통 에러 처리 로직
      console.error('Mutation 실패:', error);
      showErrorToast(error.message);
    }
  }
});

// 2. 개별 Mutation에서 전역 + 개별 로직 실행
const updateUserMutation = useMutation({
  mutationFn: updateUser,
  onSuccess: (data, variables, context) => {
    // 전역 콜백 실행
    (queryClient.getDefaultOptions().mutations?.onSuccess as any)?.(data, variables, context);
    
    // 개별 성공 처리 로직
    showToast('사용자 정보가 업데이트되었습니다');
    queryClient.invalidateQueries({ queryKey: ['users', data.id] });
  },
  onError: (error, variables, context) => {
    // 전역 콜백 실행
    (queryClient.getDefaultOptions().mutations?.onError as any)?.(error, variables, context);
    
    // 개별 에러 처리 로직
    if (error.status === 403) {
      showToast('권한이 없습니다');
    }
  }
});
```

## 대안 접근법

### Custom Hook으로 패턴 추상화
```ts
// useMutationWithDefaults.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';

export const useMutationWithDefaults = <TData, TError, TVariables, TContext>(
  options: UseMutationOptions<TData, TError, TVariables, TContext>
) => {
  const queryClient = useQueryClient();
  
  return useMutation({
    ...options,
    onSuccess: (data, variables, context) => {
      // 전역 콜백 실행
      (queryClient.getDefaultOptions().mutations?.onSuccess as any)?.(data, variables, context);
      
      // 개별 콜백 실행
      options.onSuccess?.(data, variables, context);
    },
    onError: (error, variables, context) => {
      // 전역 콜백 실행
      (queryClient.getDefaultOptions().mutations?.onError as any)?.(error, variables, context);
      
      // 개별 콜백 실행
      options.onError?.(error, variables, context);
    }
  });
};

// 사용
const updateUserMutation = useMutationWithDefaults({
  mutationFn: updateUser,
  onSuccess: (data) => {
    showToast('업데이트 완료');
    // 전역 콜백은 자동으로 실행됨
  }
});
```

<br />

# 🔗 References
- [TanStack Query v5 DefaultOptions](https://tanstack.com/query/latest/docs/framework/react/reference/QueryClient#queryclientsetdefaultoptions)
- [TypeScript Type Assertion](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions)
- [TanStack Query Mutation Callbacks](https://tanstack.com/query/latest/docs/framework/react/guides/mutations#mutation-side-effects) 
