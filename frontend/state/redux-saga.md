> 📆 2025-06-18 

## TIL: Redux Saga 정리

### 오늘 배운 것

#### 1. Redux Saga란?
- **Redux Saga**는 리덕스의 미들웨어로, 비동기 작업(API 호출, 타이머, 브라우저 캐시 접근 등)과 같은 *side effect*를 효율적으로 관리하기 위해 만들어진 라이브러리다[1][2][3].
- Redux Thunk와 달리 **제너레이터(generator)** 문법을 사용하여, 비동기 흐름을 동기적으로 작성할 수 있게 해준다[4][5].

#### 2. 동작 원리와 구조
- Saga는 마치 **별도의 쓰레드**처럼 동작하며, 리덕스 액션을 감시하고 특정 액션이 발생하면 지정된 제너레이터 함수가 실행된다[1][6][5].
- 미들웨어로 동작하기 때문에, 액션과 리듀서 사이에서 흐름을 제어한다[3].
- **rootSaga**에서 여러 개의 saga를 모아 관리할 수 있다[7].

#### 3. 주요 개념 및 API
- **takeEvery, takeLatest:** 특정 액션이 발생할 때마다(모든 액션/takeEvery) 또는 가장 마지막 액션만(takeLatest) 처리한다[8].
- **call:** 함수(주로 비동기 함수)를 동기적으로 호출한다.
- **fork:** 함수를 비동기로 호출한다.
- **put:** 액션을 디스패치한다(=dispatch).
- **take:** 특정 액션이 발생할 때까지 대기한다.
- **all:** 여러 saga를 동시에 실행한다[8][9][10].

#### 4. 장점과 특징
- **복잡한 비동기 로직**(예: 여러 API 순차 호출, 요청 취소 등)을 깔끔하게 관리할 수 있다[2][3].
- 비동기 흐름을 테스트하기 쉽고, 코드 가독성이 높아진다[1][11].
- 액션 기반으로 동작하기 때문에, redux의 상태와 액션을 자유롭게 활용할 수 있다.

#### 5. 단점 및 주의점
- **러닝 커브:** 제너레이터 문법에 대한 이해가 필요하다[12][5].
- 비동기 액션이 많아질수록 코드가 길어질 수 있다[9].
- 프로젝트의 복잡도가 낮다면 오히려 오버스펙이 될 수 있다.

---

### 느낀 점

- thunk보다 복잡한 비동기 흐름이나, 요청 취소/중복 방지 등 고급 기능이 필요할 때 saga가 유용하다는 것을 알게 됐다.
- 제너레이터와 이펙트(call, put, take 등) 개념을 더 익혀야겠다.
- 단순한 비동기 처리에는 thunk, 복잡한 비동기 플로우나 테스트가 중요한 상황에는 saga를 선택하는 것이 좋을 것 같다.

출처
[1] [React] Redux-saga 알아보기 - 공대키메라 - 티스토리 https://tech-monster.tistory.com/194
[2] 리덕스 사가(Redux Saga)란 무엇인가? - 트렌비 기술블로그 https://trenbe.github.io/2022/05/25/Redux-Saga.html
[3] [Redux] Redux-saga를 알아보자 - 기억보다 기록 - 티스토리 https://uiop5809.tistory.com/280
[4] Redux & Redux-Saga 정리 - velog https://velog.io/@rkdghwnd/Redux-Redux-Saga-%EC%A0%95%EB%A6%AC
[5] Redux Toolkit + Redux Saga 구조 만들어보기(1) - 개념, 설치 https://born-dev.tistory.com/8
[6] Saga Pattern과 redux-saga - SOSOLOG https://so-so.dev/pattern/saga-pattern-with-redux-saga/
[7] redux-saga 사용법 | 기억보다 기록을 https://kyounghwan01.github.io/blog/React/redux/redux-saga/
[8] Redux - Saga의 제너레이터 이해하기, 이펙트 https://velog.io/@bigbrothershin/Redux-Saga%EC%9D%98-%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0
[9] [Redux] Redux-Saga 원리 , 개념정리 - 뺌's 개발일지 https://invalueable.tistory.com/181
[10] redux-saga로 비동기처리와 분투하다. - GitHub https://github.com/reactkr/learn-react-in-korean/blob/master/translated/deal-with-async-process-by-redux-saga.md
[11] Redux Saga에 대해서 - velog https://velog.io/@hyunspace/about-Redux-Saga
[12] Redux thunk vs Redux Saga - velog https://velog.io/@seeh_h/Redux-thunk-vs-Redux-Saga
[13] Redux-Saga 설정 및 예제 - velog https://velog.io/@corete/Redux-Saga-%EC%84%A4%EC%A0%95-%EB%B0%8F-%EC%98%88%EC%A0%9C
[14] 리덕스 사가 - redux toolkit + redux-saga 예제로 직접 사용해보기 https://velog.io/@gygy/%EB%A6%AC%EB%8D%95%EC%8A%A4-%EC%82%AC%EA%B0%80
[15] Redux Saga를 어떻게 써야할까 - 김맥스 블로그 https://maxkim-j.github.io/posts/how-to-use-redux-saga/
[16] [문서] 처음 만난 Redux: redux-saga 사용 방법 https://www.frontoverflow.com/document/1/%EC%B2%98%EC%9D%8C%20%EB%A7%8C%EB%82%9C%20%EB%A6%AC%EB%8D%95%EC%8A%A4%20(Redux)/chapter/13/redux-saga/section/77/redux-saga%20%EC%82%AC%EC%9A%A9%20%EB%B0%A9%EB%B2%95
[17] redux-saga는 왜 써야하는거지? https://milliwonkim.github.io/docs/front-end-development/reactjs/2
[18] Redux-saga를 알아보자 - 하루 기록. https://leego.tistory.com/entry/Redux-saga%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
[19] Testing Sagas - Redux-Saga https://redux-saga.js.org/docs/advanced/Testing/
