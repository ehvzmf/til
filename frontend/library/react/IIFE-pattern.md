> 📅 Date: 2025-05-28

# 📌 Focus

즉시 실행 함수(IIFE)

<br />

# ✅ 즉시 실행 함수 표현식 (IIFE)

```tsx
{(() => {
  const x = someFunction();
  return <Component value={x} />;
})()}
```

* **`(() => { ... })()`** → 함수를 정의하고 즉시 실행
* JSX는 블록 문법(`if`, `let`, `const`)이 불가능하므로,
  JSX 내부에서 지역 변수와 블록 로직을 사용하기 위해 필요

<br />

# ✅ For what ? 

| 상황                                                | 설명                                                 |
| ------------------------------------------------- | -------------------------------------------------- |
| JSX 안에서 **변수를 선언하고 그걸 바로 사용할 때**                  | JSX는 선언문 불가 (`const`, `let`, `if`, `switch` 등 못 씀) |
| `if`, `switch`, `try/catch` 같은 **블록 스코프를 써야 할 때** | JSX 내에서는 표현식만 허용됨                                  |
| **한 덩어리의 로직을 감싸서 바로 반환**하고 싶을 때                   | 컴포넌트 분리 없이 한눈에 처리하고 싶을 때                           |

<br />

## 📦 Case 1 – voteDelta 

```tsx
{(() => {
  const voteDelta = util.getVotesDelta(oldVotes, newVotes);
  return (
    <Typography sx={{ color: util.deltaColor[voteDelta.state] }}>
      {voteDelta.label}
    </Typography>
  );
})()}
```

<br />

## 📦 Case 2 – 조건마다 다르게 렌더링

```tsx
{(() => {
  if (!user) return <LoginButton />;
  if (user.isAdmin) return <AdminDashboard />;
  return <UserDashboard />;
})()}
```

→ 조건문을 간단하게 처리 가능

<br />

## ✅ JSX에서 바로 사용하지 못하는 문법 

| ❌ JSX 내부 불가 문법        | ✅ IIFE로 가능  |
| --------------------- | ----------- |
| `if`, `else`          | `if` 가능     |
| `const`, `let`        | 지역 변수 선언 가능 |
| `switch`, `try/catch` | 모두 가능       |

→ JSX는 **"표현식(expression)"** 만 허용되므로
이런 **"문(statement)"** 를 쓰려면 함수 안에서 써야 함 → IIFE

<br />

# ✅ 가독성 고려 팁

* 한두 줄 로직이면 괜찮음
* 10줄 이상 넘어가면 별도 함수로 빼는 게 낫다
* IIFE 안에서 또 IIFE 쓰는 건 금물
* 실무에서는 "1 return 안에서 끝나는 단일 블록" 정도로 제한하는 게 깔끔함

<br />

# ✅ 리팩토링 대안

| 방식                | 설명                      |
| ----------------- | ----------------------- |
| 컴포넌트 밖에서 미리 변수 선언 | 간단한 로직이면 밖으로 뺀다         |
| 별도 함수 분리          | 복잡한 로직은 함수로 빼서 JSX에서 호출 |
| 서브 컴포넌트 분리        | 뷰 단위로 반복되면 컴포넌트화        |

```tsx
const VoteDeltaText = () => {
  const voteDelta = util.getVotesDelta(...);
  return (
    <Typography>{voteDelta.label}</Typography>
  );
};

// JSX에서
<VoteDeltaText />
```
