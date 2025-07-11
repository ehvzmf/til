> 📅 Date: 2025-07-06

# 📌 Focus
- Recoil
<br />

# 📝 Learnings
### 1. Recoil이란?
- Recoil은 페이스북에서 만든 React 상태 관리 라이브러리
- React의 상태를 더 쉽게 공유하고, 컴포넌트 트리와 무관하게 전역적으로 관리
- 기존의 Context API나 Redux보다 더 간단하고, React의 동작 방식과 매우 유사

---

### 2. 주요 개념
- **Atom**: 상태의 단위(조각). 여러 컴포넌트에서 공유, 읽기, 쓰기가 가능
- **Selector**: 파생된(계산된) 상태를 만들 때 사용. 여러 atom 또는 다른 selector의 값을 입력받아 새로운 값을 반환한다.
- **Provider**: `<RecoilRoot>`로 앱의 최상단을 감싸고 내부에 Recoil 상태 사용

---

### 3. 기본 사용 예시

````javascript name=exampleRecoil.js
import { atom, selector, useRecoilState, useRecoilValue, RecoilRoot } from 'recoil';

// atom: 상태의 단위
const countState = atom({
  key: 'countState',   // 고유 key
  default: 0,          // 초기값
});

// selector: 파생 상태
const doubleCountState = selector({
  key: 'doubleCountState',
  get: ({ get }) => get(countState) * 2,
});

// 컴포넌트 예시
function Counter() {
  const [count, setCount] = useRecoilState(countState);
  const double = useRecoilValue(doubleCountState);

  return (
    <div>
      <p>Count: {count}</p>
      <p>Double: {double}</p>
      <button onClick={() => setCount(count + 1)}>증가</button>
    </div>
  );
}

// 루트에 RecoilRoot 추가
function App() {
  return (
    <RecoilRoot>
      <Counter />
    </RecoilRoot>
  );
}
````

---

### 4. Recoil의 장점
- **간단한 API**: React 훅처럼 직관적인 사용
- **비동기 상태 관리**: selector에서 비동기 연산(Promise)을 지원
- **컴포넌트별 리렌더링 최적화**: atom 단위로 리렌더링 -> 불필요한 렌더링을 최소화
- **동적 상태 생성**: key만 다르면 동적으로 atom/selector를 생성해 사용

---

🔗 References
- [Recoil 공식 문서](https://recoiljs.org/)
