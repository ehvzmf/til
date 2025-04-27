> 📅 Date: 2025-04-27

# 📌 Focus
- UI 컴포넌트 스냅샷 테스트
- 암묵적 역할 (Implicit Role)
- 접근 가능한 이름 (Accessible Name)

<br />

# 📝 Learnings

## ✅ UI 컴포넌트 스냅샷 테스트란?

> 컴포넌트의 렌더링 결과를 **파일로 저장**하고,  
> 이후 테스트 시 **렌더링 결과가 변했는지**를 비교하는 방식

- 주요 목적: **예상치 못한 UI 변경 감지**
- Jest와 Testing Library에서 많이 사용

### ✍️ 예시

```tsx
import { render } from '@testing-library/react';
import { MyButton } from './MyButton';

test('MyButton 렌더링 스냅샷', () => {
  const { asFragment } = render(<MyButton />);
  expect(asFragment()).toMatchSnapshot();
});
```

- 처음 실행 시 스냅샷 파일(`__snapshots__`) 생성
- 이후 테스트에서 실제 렌더 결과와 스냅샷 비교
- **변경 시 승인하거나 재생성** 필요

---

## ✅ 암묵적 역할 (Implicit Role)

> HTML 요소가 **자동으로 부여받는 ARIA 역할**을 의미

- 명시적으로 `role=""` 속성을 주지 않아도,  
  **기본적으로 접근성 트리에 역할이 설정**된다.

### 📌 대표적인 암묵적 역할

| HTML 요소 | 암묵적 역할 |
|-----------|-------------|
| `<button>` | `button` |
| `<a href="#">` | `link` |
| `<input type="checkbox">` | `checkbox` |
| `<form>` | `form` |
| `<img alt="...">` | `img` |

### ✍️ 예시
```tsx
<button>Submit</button>
```
- 별도로 `role="button"`을 지정하지 않아도,  
- 스크린 리더 등은 이 요소를 `button` 역할로 인식함

> **참고**: `role`을 명시하면 기본 암묵적 역할이 덮어쓰기 될 수 있으니 주의!

---

## ✅ 접근 가능한 이름 (Accessible Name)

> 스크린 리더가 **이 요소를 어떤 이름으로 읽어야 하는지**를 정의하는 것

- **label, aria-label, alt** 속성 등으로 결정됨
- 텍스트 콘텐츠도 접근 가능한 이름이 될 수 있음
- 접근성(A11Y) 테스트의 핵심 포인트 중 하나

### 📌 접근 가능한 이름이 설정되는 방법

| 방법 | 설명 |
|------|------|
| `<label for="id">` | 폼 요소와 연결된 라벨 텍스트 |
| `aria-label="..."` | 직접 이름 지정 |
| `alt="..."` | 이미지의 대체 텍스트 |
| 요소의 텍스트 콘텐츠 | 버튼 등 직접 읽을 수 있는 텍스트 |

### ✍️ 예시

```tsx
<button>Submit Order</button> 
// 접근 가능한 이름: "Submit Order"
```

```tsx
<img src="..." alt="Profile picture" />
// 접근 가능한 이름: "Profile picture"
```

```tsx
<input type="search" aria-label="Search site" />
// 접근 가능한 이름: "Search site"
```

---

## 🧠 Summary

- 스냅샷 테스트는 **UI의 변경을 빠르게 감지**하는 데 유용하지만,  
  **변경이 잦은 컴포넌트**에는 과도한 스냅샷 관리 이슈가 생길 수 있음 → 신중히 사용
- 암묵적 역할은 **HTML 표준에 따라 자동 부여**되므로,  
  **불필요한 `role` 속성 추가를 피하는 것**이 더 깨끗한 코드
- 접근 가능한 이름은 **모든 인터랙티브 요소(UI 컴포넌트)**에 반드시 고려해야 하며,  
  **스크린 리더 사용자**를 위한 핵심 포인트임을 다시 인식

---

# 🔗 References
- [Testing Library - Snapshot Testing](https://testing-library.com/docs/example-snapshot/)
- [WAI-ARIA - Implicit ARIA Semantics](https://www.w3.org/TR/html-aria/#docconformance)
- [Accessible Name Computation (W3C)](https://www.w3.org/TR/accname/)
