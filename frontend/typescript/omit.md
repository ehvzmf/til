> 📅 Date: 2025-05-14

# 📌 Focus  
- TypeScript 유틸리티 타입 `Omit`

<br />

# 📝 Learnings

## ✅ Omit이란?

> **Omit<T, K>**는 타입 `T`에서 속성 `K`를 제거한 **새 타입을 만드는 유틸리티 타입**

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

- `T`: 원본 타입
- `K`: 제거하고 싶은 key (string literal 또는 유니언 타입)

<br />

## ✅ 기본 사용 예

```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type PublicUser = Omit<User, 'email'>;
// => { id: number; name: string }
```

- `User`에서 `email` 속성을 제거한 타입 생성

<br />

## ✅ 여러 속성 제거

```ts
type PrivateUser = Omit<User, 'email' | 'id'>;
// => { name: string }
```

- `K`는 유니언 타입으로 다중 key 제거 가능

<br />

## ✅ 함수 파라미터 타입 제어

```ts
type UserForm = Omit<User, 'id'>;

function updateUser(id: number, data: UserForm) {
  // data는 name, email만 포함
}
```

- `id`는 서버에서 사용 → 클라이언트에서 전달받는 `data`는 제외

<br />

## ✅ 실무에서 자주 쓰이는 케이스

| 케이스 | 설명 |
|--------|------|
| 수정 폼 | DB에서 받은 타입에서 ID, 생성일 등 제거 |
| 입력 타입 분리 | 전체 타입에서 일부 필드만 제외 |
| API 요청 | 전체 모델 타입 중 일부 필드만 보낼 때 |

<br />

## ✅ Omit vs Pick 비교

| 목적 | 사용 타입 | 예시 |
|------|-----------|------|
| 특정 필드 **제외** | `Omit` | `Omit<User, 'id'>` |
| 특정 필드 **선택** | `Pick` | `Pick<User, 'email'>` |

- 두 타입은 반대되는 개념

<br />

## ✅ 주의사항

- `K`에 지정한 key가 `T`에 존재하지 않으면 타입 에러 발생
- **deep Omit은 지원되지 않음** (중첩된 객체 내부 필드 제거는 별도 구현 필요)

<br />

# 🔗 References

- [TypeScript Docs – Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)
- [Type Challenges – Omit](https://github.com/type-challenges/type-challenges)

---

이 내용을 바탕으로 `Pick`, `Partial`, `Exclude`, `Record` 등 다른 유틸리티 타입도 함께 이어서 정리해볼 수 있어요.  
원하시면 다음으로 `Partial vs Omit` 비교도 정리해드릴게요! 😊
