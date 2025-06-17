> 📅 Date: 2025-06-17

## 📌 Focus

Browser Compatibility, Safari Issues 
<br />
브라우저 호환성 이슈 및 문제 해결 (Safari, IE...)

<br />

## 📝 Learnings

### 주요 브라우저 호환성 이슈

#### 1. Safari의 주요 이슈들

**Flexbox 버그**
- Safari에서 `flex-shrink: 0`이 제대로 작동하지 않는 경우가 있음
- 해결방법: 명시적으로 `min-width: 0`을 추가

```css
.flex-item {
  flex-shrink: 0;
  min-width: 0; /* Safari용 추가 */
}
```

<br />

**Date 객체 파싱 문제**
- Safari는 날짜 형식에 매우 까다로움
- `new Date('2023-01-01')` 형태는 안전하지만 `new Date('2023/01/01')`은 문제가 될 수 있음

```javascript
// ❌ Safari에서 문제가 될 수 있는 형태
const date1 = new Date('2023/01/01');

// ✅ 안전한 형태
const date2 = new Date('2023-01-01');
const date3 = new Date(2023, 0, 1); // 월은 0부터 시작
```

<br />

**CSS Grid 지원 차이**
- Safari의 이전 버전에서 CSS Grid의 일부 기능이 지원되지 않음
- `grid-gap` 대신 `gap` 사용 권장

```css
.grid-container {
  display: grid;
  gap: 20px; /* grid-gap 대신 사용 */
}
```

<br />

#### 2. 일반적인 크로스 브라우저 이슈

**이벤트 핸들링 차이**
```javascript
// ✅ 모든 브라우저에서 안전한 방법
element.addEventListener('click', function(event) {
  event.preventDefault();
  event.stopPropagation();
});
```

**CSS Vendor Prefix**
```css
.element {
  -webkit-transform: translateX(100px);
  -moz-transform: translateX(100px);
  -ms-transform: translateX(100px);
  transform: translateX(100px); /* 표준 속성은 마지막에 */
}
```

**Feature Detection**
```javascript
// Modernizr 또는 직접 구현
if ('serviceWorker' in navigator) {
  // Service Worker 지원
  navigator.serviceWorker.register('/sw.js');
}

// CSS Feature 검사
if (CSS.supports('display', 'grid')) {
  // CSS Grid 지원
}
```

<br />

### 해결 전략

#### 1. Progressive Enhancement
- 기본 기능부터 구현하고 고급 기능을 점진적으로 추가
- 모든 브라우저에서 최소한의 기능은 동작하도록 보장

<br />

#### 2. Polyfill 활용
```javascript
// ES6 Promise polyfill
if (!window.Promise) {
  // Promise polyfill 로드
}

// IntersectionObserver polyfill
if (!('IntersectionObserver' in window)) {
  // polyfill 로드
}
```

<br />

#### 3. 브라우저별 테스트 도구
- BrowserStack, Sauce Labs 등 활용
- 로컬에서는 다양한 브라우저와 버전으로 직접 테스트

<br />

#### 4. CSS Reset/Normalize
```css
/* 브라우저 기본 스타일 차이 해결 */
* {
  box-sizing: border-box;
}

html, body {
  margin: 0;
  padding: 0;
}
```

<br />

### Safari 특화 팁

**모바일 Safari 이슈**
```css
/* iOS Safari의 bounce 효과 제거 */
body {
  overscroll-behavior: none;
}

/* iOS Safari의 확대/축소 방지 */
input, textarea, select {
  font-size: 16px; /* 16px 미만이면 자동 확대됨 */
}
```

<br />

**Safari 개발자 도구 활성화**
- iPhone/iPad에서 설정 > Safari > 고급 > 웹 검사기 활성화
- Mac Safari에서 개발 메뉴 활성화

<br />

## 🔗 References

- [Can I Use](https://caniuse.com/) - 브라우저 호환성 체크
- [MDN Web Docs - Cross Browser Testing](https://developer.mozilla.org/ko/docs/Learn/Tools_and_testing/Cross_browser_testing)
- [Safari Web Technology Overview](https://developer.apple.com/safari/technology-preview/)
- [Autoprefixer](https://autoprefixer.github.io/) - CSS vendor prefix 자동 추가
- [Modernizr](https://modernizr.com/) - Feature detection 라이브러리
- [BrowserStack](https://www.browserstack.com/) - 크로스 브라우저 테스팅 도구
