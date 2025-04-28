> 📅 Date: 2025-04-28

# 📌 Focus
- React FC (Function Component)

<br />

# 📝 Learnings

## ✅ React FC
> **React.FunctionComponent(FC)**
> 함수형 컴포넌트의 props 타입을 정의
> 기본적으로 **children props를 자동 포함**
> **JSX.Element 반환을 강제**하는 타입 별칭(Type Alias)

---

## ✍️ FC의 기본 구조

```tsx
import { FC } from 'react';

const MyComponent: FC = () => {
  return <div>Hello</div>;
};
```

- 함수형 컴포넌트를 타입스크립트로 작성할 때 props 타입과 반환 타입을 동시에 관리할 수 있도록 돕는다.
- `children` prop이 자동으로 추가된다. (ReactNode 타입)
- 반환 타입은 항상 `ReactElement<any, any> | null`로 제한된다.

---

### 📦 React.FC 내부 타입 구조 (단순화)

```tsx
interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>): ReactElement<any, any> | null;
}
```
- `PropsWithChildren<P>`는 props 타입에 `children?: ReactNode`를 자동으로 추가해준다.
- 컴포넌트는 반드시 `ReactElement` 또는 `null`을 반환해야 한다.

---

## 📌 FC를 사용할 때 얻는 장점

| 장점 | 설명 |
|------|------|
| `children` 지원 | 별도 선언 없이 기본 제공 (PropsWithChildren) |
| 명시적 타입 부여 | 컴포넌트 props를 타입스크립트로 안전하게 검증 가능 |
| 반환 타입 보장 | JSX.Element 또는 null만 반환 가능해 안정성 강화 |
| 가독성 향상 | props 타입과 컴포넌트 시그니처를 한 번에 명시 |

---

## 📌 FC의 한계와 단점

| 단점 | 설명 |
|------|------|
| `children` 강제 포함 | 필요 없는 경우에도 children이 자동 추가되어 오용 가능성 |
| 반환 타입 고정 | 항상 JSX.Element 또는 null만 반환, 유연한 타입 불가 |
| 명시성 부족 | 명시적 children 제어가 어렵고 타입 세밀 제어 불리 |
| 최신 React 추세에서 지양 | 필요할 때만 FC 사용, 기본은 function + props 타입 선언 권장 |

> 요약: 실무에서는 불필요한 children 포함과 타입 유연성 저하 때문에 FC를 지양하는 흐름이 강해지고 있다.

---

## 📌 FC 남용 시 발생할 수 있는 실전 문제 예제

### 문제 1. 필요 없는 children 강제 포함

```tsx
const LogoutButton: FC = () => {
  return <button>Logout</button>;
};

// 사용 시 문제 발생 가능
<LogoutButton>잘못된 children</LogoutButton> // 오류 없음 (타입 체크 실패)
```

✅ 올바른 방식:

```tsx
const LogoutButton = () => {
  return <button>Logout</button>;
};
```
- children이 필요 없는 경우 직접 props 타입에서 제외
- 잘못된 사용 방지 가능

---

### 문제 2. children 타입 커스터마이징 어려움

```tsx
type Props = {
  title: string;
};

const Header: FC<Props> = ({ title, children }) => {
  return (
    <header>
      <h1>{title}</h1>
      {children}
    </header>
  );
};
```
- children은 기본적으로 포함되어 있어 타입 제어가 어렵다.

✅ 해결 방법:

```tsx
type Props = {
  title: string;
  children?: never; // children을 막고 싶을 때
};

const Header = ({ title }: Props) => {
  return <h1>{title}</h1>;
};
```
또는 필요하다면 명시적으로 선언:

```tsx
type Props = {
  title: string;
  children: React.ReactNode; // 명시적으로 children 타입 지정
};
```

---

## ✅ 요즘 더 권장되는 방법

```tsx
type MyComponentProps = {
  name: string;
};

const MyComponent = ({ name }: MyComponentProps) => {
  return <h1>{name}</h1>;
};
```
- props를 명시적으로 타입 지정
- 필요한 경우에만 children을 선언
- 반환 타입은 타입스크립트가 자동 추론

✅ 가독성, 타입 명확성, 유지보수성
---

## ✨ 최종 요약

| 비교 기준 | FC 사용 | function + props 타입 선언 |
|-----------|---------|----------------------------|
| children 처리 | 기본 포함 | 필요 시 명시 |
| 반환 타입 | JSX.Element 고정 | 자유롭고 유연 |
| 타입 제어 | 덜 명확 | 명시적이고 엄격 |
| 유지보수성 | 상대적으로 낮음 | 높음 |
| 추천 여부 | 프로토타이핑, 작은 컴포넌트 | 실무에서는 이 방식을 기본 |

---

# 🔗 References
- [React TypeScript Cheatsheet - Function Components](https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/function_components/)
- [Dan Abramov 트윗 - FC 사용 지양 제안](https://twitter.com/dan_abramov/status/1133878326358171650)
- [React 공식 문서 - TypeScript와 함께 사용하기](https://react.dev/learn/typescript)
