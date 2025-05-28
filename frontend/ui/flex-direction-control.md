> 📅 Date: 2025-05-28

# 📌 Focus

**Flex 레이아웃에서 순서 및 방향 제어**
(반응형 대응 시 `flex-direction`, `column-reverse`, `order` 활용)

<br />

# ✅ Flex 기본 개념 정리

```css
display: flex;
```

Flexbox: 자식 요소들을 한 줄 또는 여러 줄로 정렬하는 레이아웃 방식

<br />

### 축 설명

| 축 종류       | 설명                       |
| ---------- | ------------------------ |
| Main Axis  | 기본 정렬 방향 (row or column) |
| Cross Axis | Main의 반대 방향              |

<br />

## ✅ flex-direction

> 자식 요소의 정렬 방향을 설정

| 속성값              | 의미                |
| ---------------- | ----------------- |
| `row`            | 가로 정렬 (기본값)       |
| `row-reverse`    | 가로 반대 정렬          |
| `column`         | 세로 정렬             |
| `column-reverse` | 세로 반대 정렬 (아래 → 위) |

```css
.flex-box {
  display: flex;
  flex-direction: column-reverse;
}
```

→ 자식 요소들이 **위에서 아래가 아닌 아래에서 위로 정렬**

<br />

## ✅ order 속성

> **각 자식 요소가 렌더링되는 순서를 조절**

* 숫자가 작을수록 먼저 렌더링됨
* 기본값은 `order: 0`

```css
.item1 {
  order: 2;
}
.item2 {
  order: 1;
}
```

→ 실제 DOM 순서가 item1 → item2여도 렌더링 순서는 반대가 됨

<br />

# ✅ 반응형에서 활용하는 패턴

### 모바일(세로): `flex-direction: column`

### 데스크탑(가로): `flex-direction: row`

```tsx
<Flex
  sx={{
    flexDirection: { xs: 'column', md: 'row' },
  }}
>
  <Left />
  <Right />
</Flex>
```

→ 모바일에서는 세로 배치, PC에서는 가로 배치

<br />

### 특정 요소만 순서 변경하고 싶을 때

```tsx
<Box
  sx={{
    order: { xs: 2, md: 1 },
  }}
>
  Content A
</Box>

<Box
  sx={{
    order: { xs: 1, md: 2 },
  }}
>
  Content B
</Box>
```

→ 모바일에서는 A가 밑, B가 위 / 데스크탑에서는 원래 순서

<br />

# ✅ column-reverse vs order

| 항목       | column-reverse        | order              |
| -------- | --------------------- | ------------------ |
| 사용 위치    | 부모 컨테이너               | 자식 요소 각각           |
| 대상       | 전체 자식의 정렬 방향을 뒤집음     | 개별 요소의 렌더링 순서 제어   |
| 쓰는 경우    | 반응형에서 전체 레이아웃 방향 바꿀 때 | 특정 요소만 순서 바꾸고 싶을 때 |
| 조합 가능 여부 | ✅ 함께 사용 가능            | ✅ 함께 사용 가능         |

<br />

# ✅ Examples

### 모바일에서는 B → A 순서, PC에서는 A → B 순서

```tsx
<Flex sx={{ flexDirection: { xs: 'column-reverse', md: 'row' } }}>
  <Box>Component A</Box>
  <Box>Component B</Box>
</Flex>
```

or

```tsx
<Flex sx={{ flexDirection: 'row' }}>
  <Box sx={{ order: { xs: 2, md: 1 } }}>A</Box>
  <Box sx={{ order: { xs: 1, md: 2 } }}>B</Box>
</Flex>
```

<br />

# 🚧 Cautions

* `column-reverse`는 **순서뿐 아니라 DOM 기준 방향**도 반대로 바뀜 (스크롤 시 위로 감)
* `order`는 시멘틱 순서 유지하면서 렌더링 순서만 바꿔줌 (접근성 측면에서 유리)
* CSS 우선순위나 상위 스타일이 `order`를 덮지 않도록 주의
