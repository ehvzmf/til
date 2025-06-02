> 📅 Date: 2025-05-30

# 📌 Focus

JavaScript `reduce()` 메서드

<br />

# 📝 Learnings

## ✅ 개념
> "배열을 하나의 값으로 줄인다"

`reduce()`: 배열의 각 요소에 대해 **누적 연산**을 수행하여 **단일 값으로 줄이는** 배열 메서드 <br />
배열을 반복하며 누적(accumulate)된 결과를 만들어내는 축소(reduce) 개념 <br />
e.g. 문자열 결합, 합계 계산, 객체 변환 등

```ts
arr.reduce(callback, initialValue?)
```

<br />

## ✅ 사용 방법

```ts
array.reduce((accumulator, currentValue, currentIndex, array) => {
  // return으로 누적할 값 반환
}, initialValue);
```

| 매개변수           | 설명                                    |
| -------------- | ------------------------------------- |
| `accumulator`  | 누적된 결과값 (이전 콜백의 반환값)                  |
| `currentValue` | 현재 처리 중인 요소                           |
| `currentIndex` | 현재 요소의 인덱스                            |
| `array`        | reduce를 호출한 배열 자체                     |
| `initialValue` | accumulator의 초기값. 생략하면 배열의 첫 번째 요소 사용 |

<br />

## ✅ Examples

### 1. sum 

```ts
const nums = [1, 2, 3, 4];
const sum = nums.reduce((acc, cur) => acc + cur, 0);
// 결과: 10
```

### 2. 객체로 변환

```ts
const users = [
  { id: 1, name: 'Kim' },
  { id: 2, name: 'Lee' }
];

const userMap = users.reduce((acc, user) => {
  acc[user.id] = user.name;
  return acc;
}, {});
// 결과: { 1: 'Kim', 2: 'Lee' }
```

### 3. 중첩 배열 평탄화 (flat과 유사)

```ts
const nested = [[1, 2], [3, 4], [5]];
const flattened = nested.reduce((acc, cur) => acc.concat(cur), []);
// 결과: [1, 2, 3, 4, 5]
```

<br />

## ✅ flatMap과의 비교

| 메서드       | 설명                          | 결과 형태          |
| --------- | --------------------------- | -------------- |
| `reduce`  | 누적 작업. 유연하지만 구현 필요          | 단일 값 (객체/배열 등) |
| `flatMap` | map + flat (1단계만 평탄화) 자동 수행 | 1차원 배열         |

> `reduce`는 동작을 자유롭게 정의할 수 있어 다양한 패턴에 활용 가능
> `flatMap`은 배열 펼침에 특화된 목적 메서드

<br />

## ✅ 활용 

* 숫자/문자 합계
* 배열 → 객체 변환
* 중첩 배열 평탄화
* 그룹핑 (예: 사용자 나이별 그룹)
* 고유 값 추출
* 카운팅 (예: 투표 결과 tally)

<br />

## 💡Tips

* 꼭 `initialValue`를 주자
  → 초기값 없이 reduce를 쓰면 예상치 못한 오류가 발생할 수 있음

* 불변성 유지에 유의
  → 객체 누적 시 `acc[key] = ...`보다 **spread 연산자** 활용 추천

<br />

## 🔗 References

* [MDN - Array.prototype.reduce()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
* [JavaScript.info - Reduce](https://javascript.info/array-methods#reduce)
