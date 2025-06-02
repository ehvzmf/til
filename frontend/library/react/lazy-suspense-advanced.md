> 📅 Date: 2025-06-02

# 📌 Focus

**React.lazy와 Suspense**를 활용한 코드 스플리팅
→ 라우팅, 모달, UI 컴포넌트 등 다양한 영역에서 **코드 최적화 전략**

<br />

# 📝 Learnings

## ✅ 코드 스플리팅과 Lazy Loading

* 필요할 때만 코드를 불러와서 초기 로딩 시간 최적화
* `React.lazy()` + `Suspense`

<br />

## ✅ Remind: 

```tsx
const LazyComponent = React.lazy(() => import('./SomeComponent'));

<Suspense fallback={<div>로딩 중...</div>}>
  <LazyComponent />
</Suspense>
```

<br />

---

<br />

## ✅ 구체적인 예시 

### 1️⃣ **페이지 라우팅 분할**

```tsx
import { Suspense, lazy } from 'react';
import { Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('@/pages/Home'));
const About = lazy(() => import('@/pages/About'));

export const Router = () => (
  <Suspense fallback={<div>페이지 로딩 중...</div>}>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  </Suspense>
);
```

🟡 모든 페이지를 초기에 불러오지 않기 때문에 **초기 번들 사이즈 감소**

<br />

### 2️⃣ **모달 컴포넌트 지연 로딩**

모달처럼 자주 열리지 않는 UI는 lazy로 불러오는 것이 유리하다. 

```tsx
const ConfirmModal = lazy(() => import('@/components/modals/ConfirmModal'));

export const Page = () => {
  const [open, setOpen] = useState(false);

  return (
    <>
      <button onClick={() => setOpen(true)}>모달 열기</button>
      {open && (
        <Suspense fallback={<div>모달 로딩 중...</div>}>
          <ConfirmModal onClose={() => setOpen(false)} />
        </Suspense>
      )}
    </>
  );
};
```

✅ Modal은 열릴 때만 로딩되므로 UX에 영향 없이 성능 최적화 가능

<br />

### 3️⃣ **특정 컴포넌트 조건부 로딩 (예: 차트, 에디터)**

```tsx
const Chart = lazy(() => import('@/components/Chart'));

{isChartVisible && (
  <Suspense fallback={<div>차트 불러오는 중...</div>}>
    <Chart data={chartData} />
  </Suspense>
)}
```

✅ 복잡한 차트 컴포넌트도 조건부 로딩으로 불필요한 초기 로딩 제거

<br />

## ✅ 중첩 Suspense 활용

```tsx
<Suspense fallback={<div>전체 로딩 중</div>}>
  <Header />
  <Suspense fallback={<div>콘텐츠 로딩 중</div>}>
    <PageComponent />
  </Suspense>
</Suspense>
```

✅ 다양한 fallback을 정의해서 사용자 경험을 더 부드럽게 구성 가능

<br />

## 🔄 Summary 

| 대상          | 적용 방법                                     |
| ----------- | ----------------------------------------- |
| 페이지         | `React.lazy()`로 import 후 `Route`에서 감싸기    |
| 모달          | `open` 조건일 때만 lazy + suspense로 감싸기        |
| 무거운 컴포넌트    | `visible` 조건이나 scroll trigger 등으로 lazy 처리 |
| fallback UI | 사용자 경험 유지를 위한 로딩 화면 분기                    |

<br />

## 🔗 References

* [React 공식 문서 – Code Splitting](https://reactjs.org/docs/code-splitting.html)
* [Vite + React Lazy Load Tips](https://vitejs.dev/guide/features.html#dynamic-import)
