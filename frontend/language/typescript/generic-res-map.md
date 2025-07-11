> 📅 Date: 2025-05-20

# 📌 Focus

**TypeScript 제네릭과 조건 기반 반환 타입**
함수 파라미터에 따라 자동으로 반환 타입이 결정되는 설계 방법

<br />

# 📝 Learnings

## ✅ 목적

TypeScript 함수에서 **group 값에 따라 응답 타입을 자동으로 선택하도록**
제네릭(Generic)과 조건 매핑을 활용하여 **타입 안정성**과 **자동 완성**을 높임

<br />

## ✅ 비교: 두 가지 함수 시그니처

### 1️⃣ 잘못된 방식 – 유연하지만 위험 (`<T>(...): Promise<T>`)

```ts
<T>(period: Period, date: string, group: Group): Promise<T>
```

| 특징        | 설명                     |
| --------- | ---------------------- |
| 자유도 높음    | 호출자가 반환 타입을 마음대로 지정 가능 |
| 타입 불일치 가능 | 파라미터와 반환 타입의 연결이 끊어짐   |
| 타입 안전성 낮음 | 실수 유입 가능성 ↑            |

```ts
// 실제는 PersonResponse가 와야 하지만...
getTop10<PartyResponse>('weekly', '2024-05-01', 'person'); // ❌ 잘못된 타입
```

<br />

### 2️⃣ 권장 방식 – 제약은 있지만 안전 (`<T extends Group>(...): Promise<ResponseMap[T]>`)

```ts
<T extends Group>(
  period: Period,
  date: string,
  group: T
): Promise<ResponseMap[T]>
```

| 특징       | 설명                                      |
| -------- | --------------------------------------- |
| 타입 연결 강함 | group 값에 따라 반환 타입이 자동 결정                |
| 오타/혼동 방지 | `group='person'` → 반환: `PersonResponse` |
| 자동완성 가능  | 반환값 구조가 정확히 추론됨                         |

```ts
useTop10('weekly', '2024-05-01', 'person');  // 반환: Promise<PersonResponse>
useTop10('weekly', '2024-05-01', 'party');   // 반환: Promise<PartyResponse>
```

<br />

## ✅ 핵심 개념

### 🔹 타입 매핑 정의

```ts
type ResponseMap = {
  person: PersonResponse;
  position: PositionResponse;
  party: PartyResponse;
};
```

* 각 group 키값에 대응하는 API 응답 타입 지정

### 🔹 제네릭 파라미터 제약 (`<T extends Group>`)

```ts
<T extends Group>(group: T): ResponseMap[T]
```

* `T`는 반드시 `'person' | 'position' | 'party'` 중 하나
* `T`에 따라 **반환 타입이 동적으로 결정**

<br />

## ✅ 왜 이 방식이 필요한가?

| 항목            | 설명                           |
| ------------- | ---------------------------- |
| 타입 안정성        | 잘못된 타입으로 잘못된 데이터를 다루는 실수 방지  |
| 자동완성 지원       | 호출부에서 반환 타입 자동 추론            |
| 파라미터-응답 타입 연결 | `group` 인자와 반환 타입의 1:1 연결 유지 |
| 유지보수 용이성      | 타입 시스템이 스스로 오류 감지 가능         |

<br />

## ✅ 실제 사용 예시

```ts
const { data } = useTop10('weekly', '2024-05-01', 'party');

// TypeScript가 data를 PartyResponse로 추론함
data.parties;   // ✅ 사용 가능
data.positions; // ❌ 컴파일 에러 발생
```

* 잘못된 프로퍼티 접근 시 컴파일 타임에서 오류 발생 → **실수 사전 차단**

<br />

## ✅ 제네릭 복습: 기본 문법

```ts
function identity<T>(value: T): T {
  return value;
}

identity<string>('abc'); // T는 string
identity<number>(123);   // T는 number
```

* 호출 시점에 타입이 결정됨
* 여러 제네릭 타입을 동시에 쓰는 것도 가능

<br />

## ✅ 요약

| 항목     | 잘못된 방식                 | 권장 방식                                             |
| ------ | ---------------------- | ------------------------------------------------- |
| 타입 선언  | `<T>(...): Promise<T>` | `<T extends Group>(...): Promise<ResponseMap[T]>` |
| 타입 안전성 | ❌ 낮음                   | ✅ 높음                                              |
| 실수 방지  | ❌ 불가                   | ✅ 가능                                              |
| 자동 완성  | ❌ 미지원                  | ✅ 지원                                              |

<br />

# 🔗 References

* [TypeScript Handbook - Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
* [TS Conditional Type Mapping 공식 문서](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)
* [TypeScript Playground 예제](https://www.typescriptlang.org/play)
