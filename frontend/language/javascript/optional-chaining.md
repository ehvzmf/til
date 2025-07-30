> 📆 2025-07-30

# 📌 Focus
- Optional Chaining

<br />

# 📝 Learnings
### Optional Chaining
```js
name = data?.name;
```
- 객체의 프로퍼티가 존재하지 않을 때 에러 없이 `undefined`를 반환하는 JavaScript 문법
- 중첩된 객체에서 안전하게 값을 조회할 수 있어, 복잡한 데이터 구조를 다룰 때 유용하다.
- `&&` 연산자로 여러 번 존재 여부를 확인하는 대신 더 간결하게 표현 가능

<br />

```js
const user = {
  profile: {
    name: 'Mia',
    address: null
  }
};

// 기존 방식
const city = user && user.profile && user.profile.address && user.profile.address.city;
// 옵셔널 체이닝
const city2 = user?.profile?.address?.city;
console.log(city2); // undefined (에러 발생하지 않음)
```

// 함수 호출에도 사용 가능
```js
user.getName?.(); // getName이 없으면 undefined 반환, 에러 없음
```
<br />


# 🔗 References
- [MDN: Optional chaining (?.)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)