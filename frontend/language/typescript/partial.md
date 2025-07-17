> 📅 Date: 2025-05-20

# 📌 Focus

* TypeScript 유틸리티 타입 `Partial<T>`

<br />

# 📝 Learnings

## ✅ Partial<T>
`Partial<T>`는 **타입 `T`의 모든 속성을 선택적(optional)으로 바꾸는** 유틸리티 타입

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

* 원래 필수였던 속성들도 전부 `?`가 붙음
* 주로 **부분 업데이트, 초기 상태값 설정** 등에 사용

<br />

## ✅ 기본 사용 예

```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type UserDraft = Partial<User>;
```

→ 결과는 다음과 같음:

```ts
{
  id?: number;
  name?: string;
  email?: string;
}
```

<br />

## ✅ 실무 활용 예시

### 1. 폼 초기값

```ts
const defaultForm: Partial<User> = {
  name: 'Alice',
};
```

* 전체 User 타입을 기준으로 하되, 일부 속성만 미리 지정

<br />

### 2. 객체 패치/수정 API

```ts
function updateUser(id: number, data: Partial<User>) {
  // data는 일부 필드만 포함 가능
}
```

* `PATCH /users/:id` 같은 API에서 매우 유용

<br />

### 3. 상태 업데이트

```ts
const [user, setUser] = useState<User>({...});

setUser(prev => ({
  ...prev,
  ...partialUpdate, // Partial<User> 타입
}));
```

<br />

## ✅ Partial vs Required vs Pick vs Omit

| 유틸리티 타입       | 설명             |
| ------------- | -------------- |
| `Partial<T>`  | 모든 속성을 `?`로 변경 |
| `Required<T>` | 모든 속성을 필수로 만듦  |
| `Pick<T, K>`  | 특정 필드만 선택      |
| `Omit<T, K>`  | 특정 필드를 제외      |

<br />

## ✅ 주의할 점

* **중첩 타입에는 적용되지 않음**

```ts
type User = {
  id: number;
  profile: {
    nickname: string;
    image: string;
  };
};

type PartialUser = Partial<User>;
// profile은 그대로 필수로 유지됨
```

* 중첩까지 optional로 만들려면 `deep partial` 유틸리티가 따로 필요

<br />

## ✅ 요약

| 항목   | 설명                         |
| ---- | -------------------------- |
| 목적   | 전체 타입에서 일부 속성만 작성 가능하도록 허용 |
| 사용 예 | 수정 API, 초기값 설정, 부분 상태 변경   |
| 특징   | `T`의 모든 필드가 `optional`로 바뀜 |
| 한계   | 중첩 타입에는 기본 적용되지 않음         |

<br />

# 🔗 References

* [TypeScript Docs – Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)
* [Type Challenges – Partial](https://github.com/type-challenges/type-challenges)
