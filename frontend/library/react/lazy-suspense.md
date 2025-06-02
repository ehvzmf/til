> 📅 Date: 2025-06-02

# 📌 Focus

최적화 -> React `lazy()` & `Suspense` <br />
(컴포넌트 단위로 필요할 때만 호출 가능)

<br />

# 📝 Learnings

### 🔹Code Splitting

> 웹 앱을 빌드하면 자바스크립트 번들 하나로 묶이고, 번들이 커질수록 로딩 속도가 느려진다.
>
> -> 코드를 분할해 앱의 각 부분을 별도 파일로 나눈다.
>
> 사용자가 특정 기능을 사용하려고 할 때만 해당 코드를 로딩할 수 있다. 

👉 초기에 불필요한 코드를 줄이고 **초기 로딩 시간 개선**

<br />

### 🔹 Lazy Loading

> "필요할 때까지 코드를 불러오지 않기"
> React에서는 특정 컴포넌트를 **지연해서 import**

```tsx
const LazyComponent = React.lazy(() => import('./MyComponent'));
```

* `MyComponent`는 처음에는 로딩x
* 실제 렌더링 시점에서 네트워크로 불러온다.
* 이때 **Suspense**가 함께 필요하다. 

<br />

### 🔹 Suspense

> lazy로 불러오는 동안 **로딩 중 화면을 보여주는 역할**

```tsx
<Suspense fallback={<div>로딩 중...</div>}>
  <LazyComponent />
</Suspense>
```

<br />

---

<br />

## ✅ 코드 최적화

| 문제             | lazy & suspense로 해결 가능   |
| -------------- | ------------------------ |
| 초기 번들 크기 큼     | 페이지별 분할로 작은 단위 로딩 가능     |
| 느린 초기 렌더링      | 주요 뷰만 먼저 보여주고 나머지는 지연 로딩 |
| 모든 기능 한 번에 불러옴 | 실제로 사용하는 순간까지 기다림        |

<br />

## ✅ Tutorials

### 1. lazy()

```tsx
const MyComponent = React.lazy(() => import('./MyComponent'));
```

* 반드시 **default export**를 사용하는 컴포넌트여야 함
* `MyComponent.tsx`는 초기엔 번들에 포함되지 않음
* 실제 렌더링 시 import 발생 → 코드 다운로드됨

<br />

### 2. Suspense

```tsx
<Suspense fallback={<Loading />}>
  <MyComponent />
</Suspense>
```

* `fallback`은 컴포넌트 로딩 중 보여줄 대체 UI
* `Suspense`는 반드시 lazy 컴포넌트를 감싸야 함

<br />

---
<br />

## ✅ 전체 코드

```tsx
import { Suspense, lazy } from 'react';

const ChartPage = lazy(() => import('@/pages/ChartPage'));

export const Router = () => {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/chart" element={<ChartPage />} />
      </Routes>
    </Suspense>
  );
};
```

👉 위처럼 라우팅 시 **페이지 단위로 로딩 분할**하는 게 대표적인 최적화 방식

---

## 💡Tips 

### 1. 페이지 단위 코드 스플리팅

```tsx
const LoginPage = lazy(() => import('@/pages/LoginPage'));
const SignupPage = lazy(() => import('@/pages/SignupPage'));
```

→ 앱 시작 시 로그인 페이지만 로딩, 나머지 필요할 때 로딩

### 2. 모달이나 비동기 UI

```tsx
const ChartModal = lazy(() => import('@/modals/ChartModal'));
```

→ 열릴 때만 컴포넌트 로딩

<br />

## ✅ 주의할 점

| 항목                | 설명                           |
| ----------------- | ---------------------------- |
| default export 필수 | `lazy()`는 default export만 지원 |
| SSR 사용 불가         | `lazy()`는 클라이언트 전용           |
| 로딩 중 상태 필요        | 항상 `Suspense`로 감싸야 함         |
| 중첩 Suspense 가능    | 다양한 영역에 fallback 다르게 설정 가능   |

<br />

## ✅ 추가: React 18에서 `Suspense`의 발전

React 18부터는 `data fetching`까지 `Suspense`로 처리할 수 있게 확장됨 (예: `React Server Components`, `React Query` 연동)

<br />

## 🔗 References

* [React Docs – Lazy](https://reactjs.org/docs/code-splitting.html#reactlazy)
* [MDN – Code Splitting](https://developer.mozilla.org/en-US/docs/Glossary/Code_splitting)
