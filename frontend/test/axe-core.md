> 📅 Date: 2025-04-27

# 📌 Focus
- 접근성 자동 테스트 툴: **axe-core** 소개 및 실전 사용법

<br />

# 📝 Learnings

## ✅ axe-core란?

> **axe-core**는 **Deque Systems**에서 만든 오픈소스 **접근성(A11Y) 검사 엔진**입니다.

- 웹 페이지나 컴포넌트를 분석해서 **WCAG, ARIA 규칙**에 따라 접근성 문제를 자동 감지
- 브라우저 확장, CI(지속적 통합), 유닛 테스트 등 다양한 환경에서 사용 가능
- 빠르고 신뢰성 있는 접근성 테스트 도구

---

## 🚀 axe-core 사용 방법

### 1. 브라우저 확장 프로그램

- **Chrome, Firefox**용 `axe DevTools` 설치
- 개발자 도구(F12) → `axe DevTools` 탭에서 페이지 접근성 문제 스캔 가능

👉 **장점**: 코드 수정 없이 바로 접근성 문제 확인  
👉 **단점**: 수동 검사이므로 자동화에는 부적합

---

### 2. React 프로젝트에 통합

테스트 코드에 axe-core를 삽입해 자동 검사할 수 있습니다.

#### 설치

```bash
npm install @axe-core/react
```

#### 사용 예시

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

if (process.env.NODE_ENV !== 'production') {
  const axe = require('@axe-core/react');
  axe(React, ReactDOM, 1000);
}

ReactDOM.render(<App />, document.getElementById('root'));
```

- 개발 모드에서만 실행되게 조건 추가
- **1000ms 딜레이 후 접근성 문제를 콘솔에 경고로 출력**

---

### 3. Testing Library + jest-axe로 테스트 통합

#### 설치

```bash
npm install --save-dev jest-axe
```

#### 테스트 코드 작성

```tsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import MyComponent from './MyComponent';

expect.extend(toHaveNoViolations);

test('MyComponent는 접근성 에러가 없어야 한다', async () => {
  const { container } = render(<MyComponent />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

✅ 이 테스트가 통과하면 해당 컴포넌트에 접근성 오류가 없다는 의미입니다!

---

## 🧠 실무 적용 팁

| 적용 방식 | 장점 | 주의사항 |
|------------|------|---------|
| 브라우저 확장 | 빠른 수동 점검 | 개발 단계 한정 |
| React 개발 환경 통합 | 지속적으로 접근성 확인 | 콘솔에 출력만 |
| 유닛 테스트 통합 | CI 자동화 가능 | 커버리지가 충분히 높아야 효과적 |

- **개발할 때**는 React에서 axe-react로 빠르게 확인
- **테스트할 때**는 jest-axe로 커버리지 확대
- **릴리즈 전**에는 axe DevTools로 수동 검토 추천

---

## ✨ 요약

- **axe-core**는 접근성을 빠르게 자동 점검할 수 있는 훌륭한 툴
- **React 통합**과 **Testing Library 통합**으로 손쉽게 적용 가능
- "접근성 좋은 웹"을 만들기 위한 **기본 필수 도구**로 생각하는 게 좋음

---

# 🔗 References
- [axe-core 공식 GitHub](https://github.com/dequelabs/axe-core)
- [axe DevTools Chrome Extension](https://chrome.google.com/webstore/detail/axe-devtools-web-accessib/lhdoppojpmngadmnindnejefpokejbdd)
- [jest-axe 공식 문서](https://github.com/nickcolley/jest-axe)
