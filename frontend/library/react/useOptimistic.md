> 📆 2025-07-27

# 📌 Focus
`useOptimistic`

# 📝 Learning
- **useOptimistic**는 React에서 비동기 업데이트가 일어날 때, 실제 데이터가 반영되기 전에 UI를 미리 예측해서 보여주는 데 사용하는 훅
- 주로 사용자의 액션에 대해 빠른 피드백을 제공하고 싶은 경우, 실제 서버 응답을 기다리지 않고 UI를 먼저 업데이트 가능
- 내부적으로 낙관적 업데이트(optimistic update) 패턴을 구현할 때 유용하다.
- e.g. 리스트에 아이템 추가/삭제
- 사용 예시:
  ```js
  const [optimisticState, addOptimistic] = useOptimistic(state, (currentState, newItem) => [...currentState, newItem]);
  ```
- 첫 번째 인자는 실제 상태(state), 두 번째 인자는 상태를 변경하는 함수
- 서버 응답이 오기 전까지 optimisticState를 UI에 렌더링 > 응답 후 실제 데이터로 동기화

# 🤔 Reference  
- [React 공식문서 - useOptimistic](https://react.dev/reference/react/useOptimistic)
