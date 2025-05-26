> 📅 Date: 2025-05-26

<br />

# 📌 Focus

상태 관리 라이브러리 

<br />

# 📝 Learnings
> React에서는 `useState`, `useReducer` 등으로 컴포넌트 내부 상태를 관리하고 있다. <br />
> 여러 컴포넌트에서 상태를 공유하거나 페이지 전환 간 유지가 필요할 때는 상태 관리 라이브러리 필요

<br />

## ✅ 상태 관리가 필요한 이유 

| 필요 상황              | 설명                               |
| ------------------ | -------------------------------- |
| 전역 사용자 정보          | 로그인 상태, 사용자 ID, 토큰 등             |
| 페이지 간 상태 공유        | 예: 페이지 A에서 설정한 필터 상태를 페이지 B에서 사용 |
| 컴포넌트 간 깊은 전달 제거    | props drilling 방지                |
| 서버 상태와 클라이언트 상태 분리 | 캐시된 API 응답 vs. 사용자 입력 상태 등       |

<br />
<br />

## ✅ 대표적인 상태 관리 라이브러리
| 라이브러리                       | 특징                                      | 사용 예시                     | 사용성                                   |
| --------------------------- | --------------------------------------- | ------------------------- | ------------------------------------- |
| **Context API**             | React 내장. Provider/Consumer 구조 | 테마, 언어 설정, 간단한 전역 설정값     | 별도 설치 없음. 단일 계층에 적합                   |
| **Zustand**                 | 매우 가볍고 직관적인 함수 기반 전역 상태 라이브러리 | 모달 상태, 로그인 유저 정보, 투표 상태   | 코드량 적고, 학습 곡선 낮음. 최근 가장 인기            |
| **Redux**                   | 가장 널리 알려진 전통적인 상태 관리 도구. Flux 구조 기반 | 복잡한 앱 전역 상태, 기업용 앱        | 구조 명확. 보일러플레이트 많고 장황할 수 있음            |
| **Redux Toolkit**           | Redux의 공식 간소화 도구. 미들웨어, DevTools 지원 | 비즈니스 로직이 복잡한 대형 앱         | Redux보다 간편하지만 여전히 러닝커브 존재           |
| **Recoil**                  | atom/selector 구조로 상태 조합 지원 | 폼 데이터, 계층적 상태, 전역 필터      | React 친화적. 비동기 지원도 쉬움                 |
| **Jotai**                   | 단순한 atom 중심의 최소 상태 관리. React-first 설계 | 글로벌 폼 입력, 다크모드 설정 등       | Recoil보다 더 미니멀. 컴포넌트 단위에 적합           |
| **MobX**                    | 옵저버 기반의 반응형 상태 관리 | 복잡한 상태 변경 자동 반영이 필요한 UI   | 자동 추적, 선언적 코드. 다만 추론이 많아 디버깅 어려울 수 있음 |
| **TanStack Store** (2024\~) | tanstack-query 제작자의 초경량 상태 라이브러리 | 작은 범위의 컴포넌트 상태, 미니멀 전역 관리 | 초기 도입 단계. 파일 단위로 분리 구조 추천             |

<details>
<summary>패턴 요약</summary>

### 📌 3.1 Context + useReducer

> 가장 기본적인 전역 상태 관리 조합
> 
> 
> → 리액트 내장 기능으로 충분할 때 사용
> 

```tsx
const AppContext = createContext();
const [state, dispatch] = useReducer(reducer, initialState);
```

✔ 장점: 의존성 없음

❌ 단점: 상태가 많아지면 복잡해짐 (리렌더링 폭발)

<br />

### 📌 3.2 Redux Toolkit (RTK)

> 엄격한 구조, 액션 기반 상태 변화
> 

```ts
// slice
const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: (state) => state + 1,
  },
});

// store
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});
```

✔ 장점: 구조적, DevTools, 미들웨어, persistence 연동 강력

❌ 단점: 보일러플레이트 많음

<br />

### 📌 3.3 Zustand

> React 외부에서 전역 상태 저장소 생성 + 훅처럼 접근
> 

```
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));
```

✔ 장점
- 간결, 강력한 구독, 비동기/동기 액션 모두 편함
- boilerplate 거의 없음
- React context 없이 전역 사용 가능
- 서버 상태와 병행하기 편리

❌ 단점: 구조 강제 없음 → 팀 내 컨벤션 중요

<br />

### 📌 3.4 Recoil

> React 전용 상태 관리 라이브러리. atom 단위로 상태 분할
> 

```
const counterAtom = atom({ key: 'counter', default: 0 });
```

✔ 장점: 계층적/컴포넌트 단위 상태 분리 용이

❌ 단점: React 외부 확장 어려움, 커뮤니티 한정적

<br />

### 📌 3.5 Jotai

> Recoil과 유사하나 더 단순한 방식
> 

```
const countAtom = atom(0);
const [count, setCount] = useAtom(countAtom);
```

✔ 장점: 매우 직관적

❌ 단점: 상태 조합이나 비동기 처리에서 한계 있음

</details>

<br />

## 🏗️ 상태 관리 패턴

| 패턴 | 대표 예 | 흐름 | 특징 |
| --- | --- | --- | --- |
| Flux | Facebook Flux | View → Action → Dispatcher → Store → View | 정통 단방향 구조 |
| Redux | Redux Toolkit | View → Action → Reducer → View | 순수 함수 기반, 구조 명확 |
| Observer | Zustand, Jotai | setState → notify → render | 구조 단순, 구독 기반 |
| MVVM | Vue.js 등 | View ↔ ViewModel ↔ Model | 양방향 바인딩 중심 |
| Centralized Store | Recoil, Vuex | 컴포넌트 ↔ Store | 전역 atom/state 기반 직접 바인딩 |

<br />

### Flux 패턴

> View → Action → Dispatcher → Store → View (갱신)
> 

| 구성요소 | 역할 |
| --- | --- |
| **View** | 사용자 인터페이스 (React 등) |
| **Action** | 어떤 일이 일어났는지 명시 (예: VOTE_SUBMIT) |
| **Dispatcher** | 액션을 중계하고 스토어에 전달 |
| **Store** | 상태 저장소. 액션에 따라 내부 상태 변경 |
| **View** | 상태 변화 감지 후 다시 렌더링 |
- 단방향 데이터 흐름 패턴
- 상태는 Store 하나에서 집중 관리됨 (중앙 집중식 상태 관리)
- **Redux**: Flux를 단순화한 구조
    - `View → Action → Reducer → New State → View`
    - Dispatcher는 불필요하므로 제거
    - Reducer 함수로 상태를 명확하게 변화 → 불변성 유지
    - 상태는 항상 새로운 객체로 반환되어 추적하기 쉬움
    - 순수 함수 기반 상태 변경  (`(state, action) => newState`)

<br />

### Observer 패턴

- Subject(발신자)와 Observer(수신자)간 느슨한 연결 구조를 만든다.
- 하나의 객체 상태가 변할 때, 그 객체를 관찰하고 있는 다른 객체들에게 자동 알림
- `Subject`: 상태를 갖고 있고 변경 시 알림 발송
- `Observer`: 상태를 구독하고 있다가 알림을 받으면 반응
- `subscribe()`: 옵저버 등록
- `notify()`: Subject가 상태 변경 통지
- Observer들이 Subject를 subscribe() → Subject 내부 상태가 `set()`으로 변경 → Subject는 모든 Observer에게 notify() → Observer들은 새 상태를 보고 행동
- **Zustand:** `set()` 호출 → 상태 변경 → 구독한 컴포넌트만 리렌더링
    
    React 컴포넌트가 옵저버 역할, 내부적으로는 subscribe된 상태
    
    ```tsx
    const useStore = create((set) => ({
      count: 0,
      increment: () => set((s) => ({ count: s.count + 1 })),
    }));
    ```

<br />

<details>
<summary>상태관리를 이해하기 위한 노력</summary>

- zustand는 **전역 상태 저장소**를 만드는 함수
    - `create()` → 상태 저장소(store)를 생성하고 반환하는 훅 생성기
    - `use____Store()`를 통해 전역 메모리에 저장된 상태 객체를 구독하고, 일부 값이나 액션을 사용
    - `useState`/`useReducer` 의 한계 해결
        - props로 내려주는 대신 전역에서 자유롭게 접근 가능하고, 리렌더 기반 대신 필요한 부분만 선택적으로 구독 가능
    - 작동 구조
        
        상태 생성 → 컴포넌트에서 기존 store를 구독 → 상태를 읽는 컴포넌트만 리렌더 
        
        ```tsx
        // 내부적으로 context/provider를 사용하지 않고 자체 메모리에 전역 상태를 만든다.
        // 리액트 컴포넌트는 이걸 구독해서 사용하게 된다. 
        export const useModalStore = create((set) => ({
          isOpen: false,
          open: () => set({ isOpen: true }),
        }));
        
        // store에 접근하는 인터페이스. 리액트 컴포넌트에서 기존 store를 구독 
        const { isOpen, open } = useModalStore();
        ```
</details>
  
<br /><br />

## ✅ 상태 관리 vs 서버 상태 관리

| 구분       | 예시                 | 대표 도구                        |
| -------- | ------------------ | ---------------------------- |
| 클라이언트 상태 | 사용자 입력값, 다크모드 설정 등 | Zustand, Recoil, Redux       |
| 서버 상태    | API 응답 데이터         | React Query (TanStack Query) |

→ 일반적으로 둘을 **분리**해서 관리하는 것이 유지보수에 유리

<br />

## ✅ 상태 관리 도구 선택 기준

| 고려 항목   | 설명                                    |
| ------- | ------------------------------------- |
| 팀 규모    | Redux는 대규모 팀에 명확함, Zustand는 소규모에 적합   |
| 학습 비용   | Zustand, Jotai는 빠르게 시작 가능             |
| 복잡도     | MobX는 자동 추적으로 복잡한 앱도 간결하게 표현          |
| 생태계     | Redux는 미들웨어, devtool, plugin 등 다양     |
| SSR/CSR | Recoil, Jotai 등 일부 도구는 SSR과 호환성 고려 필요 |

<br />

## ✅ 참고사항 

* 상태는 목적별로 분리 관리: `authStore`, `modalStore`, `settingsStore` 등
* 상태 공유가 적고 단순하면 `useContext + useReducer`도 충분
* 서버 상태는 가능하면 React Query로 위임하고, 캐시/동기화 자동화
* 저장이 필요한 상태는 `zustand + persist`, `redux-persist` 등과 함께 사용
