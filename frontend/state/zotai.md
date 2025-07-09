> 📅 Date: 2025-07-09

# 📌 Focus
- zotai (프론트엔드 전역 상태관리 라이브러리)
<br />

# 📝 Learnings
- 이름의 뜻: “zotai”는 “조타이”라고 읽으며, 일본어의 “状態(じょうたい, 상태)”에서 따온 이름. 전역 상태(state)를 쉽게 관리하기 위해 만들어진 라이브러리.
- 설치 방법  
  ```bash
  npm install zotai
  ```
- import 방법  
  ```javascript
  import { createStore, useStore } from 'zotai'
  ```
- 기본 사용법  
  1. 전역 상태 스토어 생성  
     ```javascript
     // counter.js
     import { createStore } from 'zotai'
     export const counterStore = createStore(0)
     ```
  2. 컴포넌트에서 상태 사용  
     ```javascript
     import { useStore } from 'zotai'
     import { counterStore } from './counter'

     function Counter() {
       const [count, setCount] = useStore(counterStore)
       return (
         <div>
           <span>{count}</span>
           <button onClick={() => setCount(count + 1)}>증가</button>
         </div>
       )
     }
     ```
  3. 여러 컴포넌트에서 동일한 스토어 사용  
     - 여러 컴포넌트가 동일한 store 인스턴스를 import해서 사용하면, 상태가 전역적으로 공유됨.
     - 상태 변경 시 관련된 모든 컴포넌트가 자동으로 리렌더링 됨.
  4. 비동기 및 파생 상태  
     - 비동기 데이터 로딩 또는 파생 상태(derived state)는 useEffect와 조합하거나, store에서 직접 계산 로직 추가 가능.
     ```javascript
     // 파생 상태 예시
     import { createStore } from 'zotai'
     export const userStore = createStore({ name: '', age: 0 })
     export const isAdultStore = createStore((get) => get(userStore).age >= 20)
     ```
- 특징  
  - API가 간결하고 직관적임.  
  - React의 useState와 유사한 사용성.  
  - Redux, Recoil 등 기존 상태관리보다 러닝커브가 낮음.  
  - 타입스크립트 지원.  
  - 불필요한 상태변경 방지(동일 값 set시 리렌더링 없음).  
  - 미들웨어, 비동기 처리, 파생 상태 등 확장성 제공.
<br />

# 🔗 References
- [zotai 공식 문서](https://zotai.dev/)
- [zotai GitHub](https://github.com/zotai/zotai)
