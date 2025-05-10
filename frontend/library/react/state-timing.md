> 📅 Date: 2025-05-10

# 📌 Focus

* `useState`의 비동기적 특성과 관련된 상태값 접근 문제 해결

<br />

# 📝 Learnings

## ✅ React의 상태 업데이트는 비동기적으로 동작함

> `useState`로 선언된 상태를 `setState()`로 업데이트할 때,
> 해당 변경은 **즉시 반영되지 않고 다음 렌더 사이클에서 처리됨**

### 예시

```tsx
setValue('newValue');
console.log(value); // 여전히 이전 값이 출력됨
```

* `setValue()`는 내부적으로 상태 변경을 **스케줄링**만 함
* `console.log(value)`는 여전히 이전 렌더 기준의 값

<br />

## 🔥 실제 문제 사례: setState 직후 값 활용

```tsx
setPendingUserData({ ...verify.data, gender, ageRange });
localStorage.setItem('pending_user', JSON.stringify(pendingUserData));
```

* 상태 업데이트 직후, `pendingUserData`를 로컬 스토리지에 저장하려고 시도
* 하지만 `pendingUserData`는 여전히 **이전 값(혹은 null)** 이기 때문에 잘못된 정보 저장됨

<br />

## ✅ 해결 방법: 직접 사용할 값을 변수로 먼저 정의

```tsx
const newPending = { ...verify.data, gender, ageRange };
setPendingUserData(newPending);
localStorage.setItem('pending_user', JSON.stringify(newPending));
```

* `newPending` 객체를 **중간 변수로 정의**하여
* `setPendingUserData()`와 `localStorage` 모두에서 **동일한 값 사용**
* React의 **비동기 업데이트 지연 문제**를 회피

<br />

## ✅ 왜 상태값을 즉시 쓸 수 없는가?

| 항목                    | 설명                                          |
| --------------------- | ------------------------------------------- |
| `setState()`의 본질      | 상태를 즉시 변경하지 않고 리액트의 렌더 사이클에 맞춰 나중에 반영       |
| 동기적 코드 흐름             | 상태 변경 직후의 `state` 값은 여전히 이전 값을 유지           |
| useEffect 활용해도 해결 불가능 | 단일 함수 안에서 즉시 값을 써야 할 경우에는 `useEffect`도 부적절함 |

<br />

## ✅ 추가 예제: form 데이터 → 상태 업데이트 후 API 호출

```tsx
const [formData, setFormData] = useState({ name: '', email: '' });

const handleSubmit = () => {
  const payload = { name: 'Lee', email: 'lee@example.com' };
  setFormData(payload);
  
  sendDataToServer(payload); // ✅ formData 대신 payload 직접 사용
};
```

* `setFormData(payload)`는 비동기
* 따라서 `formData`는 아직 이전 값이므로
* **새로운 값(payload)을 직접 API에 전달**

<br />

## ✅ 요약

| 주제       | 내용                                    |
| -------- | ------------------------------------- |
| 비동기성의 원인 | `useState`는 렌더링 사이클 기준으로 동작함          |
| 흔한 실수    | `setState` 직후 상태값을 사용함                |
| 해결 방법    | 새 값을 변수에 저장해서 상태와 외부 로직 모두에 사용        |
| 실무 활용    | localStorage, API 요청, DOM 조작 등에 특히 중요 |

<br />

# 🔗 References

* [React Docs – State Updates May Be Asynchronous](https://react.dev/learn/state-as-a-snapshot)
* [Dan Abramov – Overreacted Blog](https://overreacted.io/)

* `useEffect`와 관련된 상태 동기화 문제
* 상태값을 안전하게 처리하기 위한 패턴 (ref, batching 등)

이런 주제도 관심 있으신가요? 😊
