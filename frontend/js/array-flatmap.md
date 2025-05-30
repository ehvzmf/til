> 📅 Date: 2025-05-30

# 📌 Focus

**Array.prototype.flatMap()**

<br />

# 📝 Learnings

## ✅ 개념

`flatMap()`: JavaScript 배열 메서드 <br />
**각 배열 요소에 대해 매핑 함수를 적용하고, 그 결과를 평탄화(flatten)** 해서 **새로운 배열을 반환** <br />

```js
arr.flatMap(callbackFn)
```

* 기본적으로 `.map()`과 `.flat()`을 한번에 처리
* 중첩 배열을 **한 단계만 평탄화**
* ES2019(ES10)부터 표준 도입

<br />

## ✅ flatMap의 동작 방식

```js
array.flatMap((element, index, array) => {
  return newArrayElement;
});
```

* `element`: 현재 요소
* `index`: 현재 요소의 인덱스
* `array`: 호출한 원본 배열

※ `newArrayElement`는 반드시 배열 또는 배열 유사 구조를 반환해야 한다.

<br />

## ✅ flatMap 예제

### 📌 1: 단순 사용

```js
[1, 2, 3].flatMap(x => [x, x * 2]);
// 결과: [1, 2, 2, 4, 3, 6]
```

- `.map()`과의 차이 :

  ```js
  [1, 2, 3].map(x => [x, x * 2]);
  // 결과: [[1, 2], [2, 4], [3, 6]] → 2차 배열
  ```

  → `flatMap()`은 한 단계만 평탄화

<br />

### 📌 2: 문자열 분해

```js
["hello world", "open ai"].flatMap(str => str.split(" "));
// 결과: ["hello", "world", "open", "ai"]
```

### 📌 3: 조건에 따른 필터 + 변환

```js
[1, 2, 3, 4].flatMap(n => (n % 2 ? [n] : []));
// 결과: [1, 3]
```

→ `.filter()` + `.map()`을 합친 효과

<br />

## ✅ 장점

* `flat`은 "납작하게 만든다"는 뜻 → 중첩 배열의 깊이를 줄임
* `map()`만 사용하면 중첩 배열이 생기기 쉬움 → 이를 자동으로 1단계 평탄화
* `reduce()`와 비교해 **더 읽기 쉬운 선언형 방식**

<br />

## ✅ flatMap vs map + flat

```js
arr.map(...).flat() // 두 번 호출
arr.flatMap(...)    // 한 번 호출 (성능적으로 이점)
```

단, `flatMap()`은 깊이 1단계만 평탄화하므로 2단계 이상은 `flat(depth)` 사용 필요.

<br />

## ✅ 활용 사례

| 상황                 | flatMap 쓰는 이유    |
| ------------------ | ---------------- |
| 문자열 분해 후 단어 배열 만들기 | map + flat 축약 가능 |
| 중첩된 배열 단일화         | 코드 간결화           |
| 조건부 필터링 + 매핑 동시 적용 | 코드 효율성 및 가독성     |
| 데이터 트리구조 변형        | 계층 평탄화 시 유용      |

<br />

## ✅ 구체적인 활용 예시 

### API 응답 리스트에서 태그 추출

```js
const posts = [
  { title: "Post1", tags: ["react", "frontend"] },
  { title: "Post2", tags: ["typescript", "frontend"] },
];

const allTags = posts.flatMap(post => post.tags);
// 결과: ["react", "frontend", "typescript", "frontend"]
```

### 테이블에 중첩 리스트 렌더링 준비

```js
const categories = [
  { id: 1, items: ["A", "B"] },
  { id: 2, items: ["C"] },
];

const allItems = categories.flatMap(cat =>
  cat.items.map(item => ({ catId: cat.id, item }))
);
// 결과: [{catId: 1, item: "A"}, ...]
```

<br />

## ⚠️ Caution

* `flatMap()`은 깊이 1단계만 평탄화
* 반환값이 배열이 아닐 경우 작동하지 않음
* `undefined`, `null`을 명시적으로 반환하면 평탄화 후 제거됨

```js
[1, 2, 3].flatMap(n => []);
// 결과: []
```

<br />

# 🔗 References

* [MDN - flatMap](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap)
* [TC39 Proposal - flatMap](https://github.com/tc39/proposal-flatMap)
