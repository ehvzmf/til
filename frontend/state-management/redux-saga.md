> 📆 2025-06-18 

# 📌 Focus
Redux Saga

<br />

# 📝 Learnings

## 1. Redux Saga란?
- Redux의 미들웨어
- 비동기 작업(API 호출, 타이머, 브라우저 캐시 접근 등)과 같은 *side effect*를 효율적으로 관리하기 위해 만들어진 라이브러리
- Redux Thunk와 달리 제너레이터(generator) 문법을 사용하여, 비동기 흐름을 동기적으로 작성할 수 있다. 

## 2. 동작 원리와 구조
- Saga는 마치 **별도의 쓰레드**처럼 동작하며, 리덕스 액션을 감시하고 특정 액션이 발생하면 지정된 제너레이터 함수가 실행된다.
- 미들웨어로 동작하기 때문에, 액션과 리듀서 사이에서 흐름을 제어한다.
- **rootSaga**에서 여러 개의 saga를 모아 관리할 수 있다.

## 3. 주요 개념 및 API
- **takeEvery, takeLatest:** 특정 액션이 발생할 때마다(모든 액션/takeEvery) 또는 가장 마지막 액션만(takeLatest) 처리
- **call:** 함수(주로 비동기 함수)를 동기적으로 호출
- **fork:** 함수를 비동기로 호출
- **put:** 액션을 디스패치(=dispatch)
- **take:** 특정 액션이 발생할 때까지 대기
- **all:** 여러 saga를 동시 실행

## 4. 장점과 특징
- **복잡한 비동기 로직**(예: 여러 API 순차 호출, 요청 취소 등)을 깔끔하게 관리
- 비동기 흐름을 테스트하기 쉽고, 코드 가독성 향상
- 액션 기반으로 동작하기 때문에, redux의 상태와 액션을 자유롭게 활용할 수 있다.

## 5. 단점 및 주의점
- **러닝 커브:** 제너레이터 문법에 대한 이해가 필요
- 비동기 액션이 많아질수록 코드가 길어질 수 있다. 
- 프로젝트의 복잡도가 낮다면 오히려 오버스펙

<br />

# 🔗 References
- [Redux-saga docs](https://redux-saga.js.org)
- [React)Redux-saga 사용해보기](https://velog.io/@blackeichi/Redux-saga-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0)
