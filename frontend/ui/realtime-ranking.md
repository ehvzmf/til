> 📅 Date: 2025-04-10

# 📌 Focus
- 실시간 랭킹 UI 구현  
- WebSocket, SSE, 폴링  
- React Transition Group, react-flip-toolkit

<br />

# 📝 Learnings
- **실시간 데이터 업데이트 방식**  
  - WebSocket/Socket.IO: 양방향 통신으로 즉각 데이터 반영  
  - SSE: 단방향 데이터 스트리밍, HTTP 기반으로 간단하게 구현  
  - 폴링: 일정 주기로 서버에 요청, 구현은 간단하나 요청이 많음

- **UI 애니메이션 및 재정렬 효과**  
  - React Transition Group: 아이템 입/퇴장, 위치 변경 시 애니메이션 적용  
  - react-flip-toolkit: 목록 재정렬 시 자연스러운 전환 효과 제공

- **전체 구현 흐름**  
  1. 데이터 수집: WebSocket, SSE, 또는 폴링으로 실시간 데이터 수신  
  2. 상태 관리: React 상태 혹은 Redux로 데이터 관리  
  3. UI 애니메이션: 변경된 순위에 애니메이션 적용  
  4. 렌더링: 최적화된 방식으로 UI 업데이트

- **코드 예제 (React Transition Group 사용)**

```tsx
import React from 'react';
import { CSSTransition, TransitionGroup } from 'react-transition-group';

const RankingList = ({ rankings }) => (
  <TransitionGroup>
    {rankings.map((item, index) => (
      <CSSTransition key={item.id} timeout={500} classNames="list-item">
        <div>
          <span>{index + 1}. </span>
          <span>{item.name}</span>
          <span>{item.score}</span>
        </div>
      </CSSTransition>
    ))}
  </TransitionGroup>
);

export default RankingList;
```

<br />

# 🔗 References
- [React Transition Group](https://reactcommunity.org/react-transition-group)
- [react-flip-toolkit](https://github.com/aholachek/react-flip-toolkit)
- [Socket.IO](https://socket.io)
