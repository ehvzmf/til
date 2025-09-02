> 📅 Date: 2025-09-02

# 📌 Focus
- CSS env() 함수
- 환경 변수 활용법
- CSS 환경 설정 기능

<br />

# 📝 Learnings

## CSS `env()` 함수 & 환경 변수
- CSS `env()` 함수는 사용자 에이전트(브라우저)가 정의한 환경 변수에 접근할 수 있게 해주는 함수
- 다양한 디바이스와 화면 형태에 대응 가능
- 주로 모바일 디바이스의 노치, 다이나믹 아일랜드, 폴더블 디스플레이 등의 물리적 제약사항을 처리할 때 사용

```css
/* 기본 문법 */
property: env(environment-variable, fallback-value);

/* 예시 */
padding-top: env(safe-area-inset-top, 20px);
```

<br />

### 브라우저 지원 현황
- iOS Safari 11.2+ (iPhone X 출시와 함께 도입)
- Chrome 69+
- Firefox 67+
- Edge 79+

<br />

## Safe Area Inset 변수들

### 핵심 환경 변수
모바일 디바이스의 물리적 제약(노치, 홈 인디케이터, 카메라 등)을 피하기 위한 안전 영역 정보를 제공

```css
/* 4가지 기본 safe area inset */
.container {
  padding-top: env(safe-area-inset-top);       /* 상단 안전 영역 */
  padding-right: env(safe-area-inset-right);   /* 우측 안전 영역 */
  padding-bottom: env(safe-area-inset-bottom); /* 하단 안전 영역 */
  padding-left: env(safe-area-inset-left);     /* 좌측 안전 영역 */
}

/* 한번에 적용 */
.safe-container {
  padding: env(safe-area-inset-top) 
           env(safe-area-inset-right) 
           env(safe-area-inset-bottom) 
           env(safe-area-inset-left);
}
```

### 실제 디바이스별 값들

- iPhone 14 Pro (세로): top 59px, bottom 34px
- iPhone 14 Pro (가로): left/right 59px, bottom 21px
- 디바이스마다 다르니까 하드코딩하지 말고 env() 쓰자

<details>
  <summary>코드 참고</summary>
  
  #### iPhone 14 Pro (세로)
  - `safe-area-inset-top`: 59px (다이나믹 아일랜드)
  - `safe-area-inset-bottom`: 34px (홈 인디케이터)
  - `safe-area-inset-left`: 0px
  - `safe-area-inset-right`: 0px
  
  #### iPhone 14 Pro (가로)
  - `safe-area-inset-top`: 0px
  - `safe-area-inset-bottom`: 21px
  - `safe-area-inset-left`: 59px
  - `safe-area-inset-right`: 59px
  
  #### Galaxy Z Fold 4 (펼친 상태)
  - 브라우저와 모드에 따라 다양한 값
  - 일반적으로 접힘 부분을 고려한 inset 제공
</details>

<br />

## viewport-fit 설정
`env()` 함수가 제대로 작동하려면 viewport meta 태그에 `viewport-fit=cover` 설정 필수!!

```html
<!-- env() 함수 사용을 위한 필수 설정 -->
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">

<!-- viewport-fit 옵션들 -->
<!-- auto (기본값): 브라우저가 결정 -->
<meta name="viewport" content="viewport-fit=auto">

<!-- contain: 안전 영역 내에서만 표시 -->
<meta name="viewport" content="viewport-fit=contain">

<!-- cover: 전체 화면 사용, env()로 안전 영역 처리 -->
<meta name="viewport" content="viewport-fit=cover">
```

### viewport-fit 동작 차이

#### viewport-fit=contain
```css
/* 브라우저가 자동으로 안전 영역 확보 */
/* env() 값들이 모두 0이 될 가능성 높음 */
body {
  /* 이미 안전 영역 내에 있으므로 추가 패딩 불필요 */
  padding: 0;
}
```

#### viewport-fit=cover
```css
/* 전체 화면 사용, 개발자가 직접 안전 영역 처리 */
body {
  /* env() 함수로 수동 처리 필요 */
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
}
```

<br />

## 실전 패턴

### 헤더/네비게이션 바
```css
.header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  
  /* 기본 높이 + 상단 안전 영역 */
  height: calc(60px + env(safe-area-inset-top));
  padding-top: env(safe-area-inset-top);
  
  background: #ffffff;
  z-index: 100;
}

.header-content {
  height: 60px;
  display: flex;
  align-items: center;
  padding: 0 16px;
}

/* 헤더 때문에 가려지는 콘텐츠 방지 */
.main-content {
  margin-top: calc(60px + env(safe-area-inset-top));
}
```

### 하단 탭 바 / FAB
```css
.bottom-tab-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  
  /* 기본 높이 + 하단 안전 영역 */
  height: calc(70px + env(safe-area-inset-bottom));
  padding-bottom: env(safe-area-inset-bottom);
  
  background: #ffffff;
  border-top: 1px solid #e0e0e0;
}

.tab-content {
  height: 70px;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

/* FAB (Floating Action Button) */
.fab {
  position: fixed;
  bottom: calc(20px + env(safe-area-inset-bottom));
  right: calc(20px + env(safe-area-inset-right));
  
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: #007AFF;
  z-index: 1000;
}
```

### 전체 화면 모달
```css
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  
  /* 안전 영역까지 덮기 */
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
  
  background: rgba(0, 0, 0, 0.5);
  z-index: 9999;
}

.modal-content {
  /* 실제 콘텐츠는 안전 영역 내에서 표시 */
  background: white;
  border-radius: 12px;
  padding: 24px;
  margin: 20px;
  
  /* 최대 높이는 안전 영역 고려 */
  max-height: calc(100vh - env(safe-area-inset-top) - env(safe-area-inset-bottom) - 40px);
  overflow-y: auto;
}
```

### 사이드바 / 드로어
```css
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 280px;
  height: 100vh;
  
  /* 상하 안전 영역 고려 */
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  
  background: #f8f9fa;
  transform: translateX(-100%);
  transition: transform 0.3s ease;
  z-index: 1000;
}

.sidebar.open {
  transform: translateX(0);
}

/* 가로 모드에서 좌우 안전 영역도 고려 */
@media (orientation: landscape) {
  .sidebar {
    left: env(safe-area-inset-left);
    padding-left: 0;
  }
}
```

<br />

## `max()`, `min()`, `clamp()`와의 조합

### 최소값 보장
```css
.container {
  /* 최소 20px는 확보하되, 안전 영역이 더 크면 그 값 사용 */
  padding-top: max(20px, env(safe-area-inset-top));
  padding-bottom: max(20px, env(safe-area-inset-bottom));
}

.header {
  /* 헤더 높이: 최소 50px, 안전 영역 포함 최대 120px */
  height: clamp(50px, calc(50px + env(safe-area-inset-top)), 120px);
}
```

### 복잡한 계산
```css
.responsive-spacing {
  /* 기본 패딩 + 안전 영역, 최소 16px 보장 */
  padding: max(16px, calc(16px + env(safe-area-inset-top))) 
           max(24px, calc(24px + env(safe-area-inset-right)))
           max(16px, calc(16px + env(safe-area-inset-bottom)))
           max(24px, calc(24px + env(safe-area-inset-left)));
}

.dynamic-margin {
  /* 뷰포트 너비에 따른 마진 + 안전 영역 */
  margin-left: max(
    env(safe-area-inset-left),
    calc((100vw - 1200px) / 2)
  );
  margin-right: max(
    env(safe-area-inset-right),
    calc((100vw - 1200px) / 2)
  );
}
```

<br />

## CSS 커스텀 속성(변수)과 env() 조합

### 디자인 토큰으로 활용
```css
:root {
  /* 기본 디자인 토큰 */
  --header-height: 60px;
  --tab-bar-height: 70px;
  --border-radius: 12px;
  --primary-color: #007AFF;
  
  /* 안전 영역 기반 계산된 값들 */
  --safe-header-height: calc(var(--header-height) + env(safe-area-inset-top));
  --safe-tab-height: calc(var(--tab-bar-height) + env(safe-area-inset-bottom));
  
  /* 콘텐츠 영역 계산 */
  --content-height: calc(
    100vh - 
    var(--safe-header-height) - 
    var(--safe-tab-height)
  );
  
  /* 최소 터치 영역 + 안전 영역 */
  --min-touch-area: max(44px, calc(44px + env(safe-area-inset-bottom)));
}

.app-layout {
  display: grid;
  grid-template-rows: var(--safe-header-height) 1fr var(--safe-tab-height);
  height: 100vh;
}

.scrollable-content {
  height: var(--content-height);
  overflow-y: auto;
}
```

### 테마별 안전 영역 처리
```css
/* 라이트 테마 */
[data-theme="light"] {
  --safe-bg-color: #ffffff;
  --safe-header-bg: rgba(255, 255, 255, 0.95);
}

/* 다크 테마 */
[data-theme="dark"] {
  --safe-bg-color: #000000;
  --safe-header-bg: rgba(0, 0, 0, 0.95);
}

.theme-header {
  background: var(--safe-header-bg);
  backdrop-filter: blur(20px);
  padding-top: env(safe-area-inset-top);
}
```

---

## CSS Houdini와 @property

### 환경 변수 타입 정의
```css
/* 안전 영역 값의 타입 명시 */
@property --safe-top {
  syntax: '<length>';
  initial-value: 0px;
  inherits: false;
}

@property --safe-bottom {
  syntax: '<length>';
  initial-value: 0px;
  inherits: false;
}

:root {
  --safe-top: env(safe-area-inset-top, 0px);
  --safe-bottom: env(safe-area-inset-bottom, 0px);
}

/* 애니메이션 가능한 안전 영역 */
.animated-header {
  padding-top: var(--safe-top);
  transition: padding-top 0.3s ease;
}
```

---

## Container Queries와 env() 조합

### 컨테이너 기반 안전 영역 처리
```css
.card-container {
  container-type: inline-size;
  padding: env(safe-area-inset-top) 16px 16px;
}

/* 컨테이너 크기에 따른 안전 영역 조정 */
@container (width > 768px) {
  .card-container {
    /* 태블릿에서는 안전 영역 무시하고 고정 패딩 */
    padding: 24px;
  }
}

@container (width <= 480px) {
  .card-container {
    /* 모바일에서는 안전 영역 + 추가 여백 */
    padding: calc(env(safe-area-inset-top) + 8px) 12px 12px;
  }
}
```

---

## Feature Queries (@supports)

### env() 지원 여부 확인
```css
/* env() 미지원 브라우저용 폴백 */
.header {
  padding-top: 20px; /* 기본값 */
}

/* env() 지원 브라우저 */
@supports (padding: env(safe-area-inset-top)) {
  .header {
    padding-top: env(safe-area-inset-top, 20px);
  }
}

/* 복합 조건 */
@supports (padding: env(safe-area-inset-top)) and (height: 100dvh) {
  .modern-layout {
    height: 100dvh;
    padding-top: env(safe-area-inset-top);
  }
}
```

### Progressive Enhancement
```css
.progressive-safe-area {
  /* Level 1: 기본 패딩 */
  padding: 20px;
}

@supports (padding: env(safe-area-inset-top)) {
  .progressive-safe-area {
    /* Level 2: env() 지원 */
    padding-top: env(safe-area-inset-top, 20px);
  }
}

@supports (padding: max(env(safe-area-inset-top), 20px)) {
  .progressive-safe-area {
    /* Level 3: max() + env() 지원 */
    padding-top: max(env(safe-area-inset-top), 20px);
  }
}
```

---

## 10) Media Queries와의 연계

### 디바이스 방향별 처리
```css
/* 세로 모드 */
@media (orientation: portrait) {
  .orientation-header {
    padding-top: env(safe-area-inset-top);
    padding-left: 16px;
    padding-right: 16px;
  }
}

/* 가로 모드 */
@media (orientation: landscape) {
  .orientation-header {
    padding-top: max(8px, env(safe-area-inset-top));
    padding-left: max(16px, env(safe-area-inset-left));
    padding-right: max(16px, env(safe-area-inset-right));
  }
}

/* 화면 크기별 안전 영역 처리 */
@media (max-width: 480px) {
  .mobile-container {
    /* 작은 화면에서는 안전 영역 + 최소 여백 */
    padding: env(safe-area-inset-top, 0) 
             max(16px, env(safe-area-inset-right, 0))
             max(16px, env(safe-area-inset-bottom, 0))
             max(16px, env(safe-area-inset-left, 0));
  }
}

@media (min-width: 768px) {
  .tablet-container {
    /* 태블릿에서는 고정 패딩 선호 */
    padding: 24px;
  }
}
```

---

## JavaScript와의 연계

### 환경 변수 값 읽기
```javascript
// CSS 환경 변수 값 읽기
function getSafeAreaInsets() {
  const computedStyle = getComputedStyle(document.documentElement);
  
  return {
    top: computedStyle.getPropertyValue('--safe-area-inset-top') || 
         getComputedStyle(document.body).getPropertyValue('env(safe-area-inset-top)') || '0px',
    right: computedStyle.getPropertyValue('--safe-area-inset-right') || '0px',
    bottom: computedStyle.getPropertyValue('--safe-area-inset-bottom') || '0px',
    left: computedStyle.getPropertyValue('--safe-area-inset-left') || '0px'
  };
}

// 더 정확한 방법: 테스트 엘리먼트 생성
function measureSafeAreaInsets() {
  const testEl = document.createElement('div');
  testEl.style.cssText = `
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    padding-top: env(safe-area-inset-top);
    padding-right: env(safe-area-inset-right);
    padding-bottom: env(safe-area-inset-bottom);
    padding-left: env(safe-area-inset-left);
    pointer-events: none;
    opacity: 0;
  `;
  
  document.body.appendChild(testEl);
  const computed = getComputedStyle(testEl);
  const insets = {
    top: parseInt(computed.paddingTop),
    right: parseInt(computed.paddingRight),
    bottom: parseInt(computed.paddingBottom),
    left: parseInt(computed.paddingLeft)
  };
  document.body.removeChild(testEl);
  
  return insets;
}
```

### 동적 CSS 업데이트
```javascript
// 안전 영역 기반 동적 스타일링
class SafeAreaManager {
  constructor() {
    this.insets = { top: 0, right: 0, bottom: 0, left: 0 };
    this.init();
  }
  
  init() {
    this.updateInsets();
    
    // 방향 변경 시 재측정
    window.addEventListener('orientationchange', () => {
      setTimeout(() => this.updateInsets(), 100);
    });
    
    // 리사이즈 시 재측정
    window.addEventListener('resize', () => this.updateInsets());
  }
  
  updateInsets() {
    this.insets = measureSafeAreaInsets();
    this.applyDynamicStyles();
    this.dispatchUpdateEvent();
  }
  
  applyDynamicStyles() {
    // CSS 커스텀 속성 업데이트
    document.documentElement.style.setProperty(
      '--js-safe-area-top', `${this.insets.top}px`
    );
    document.documentElement.style.setProperty(
      '--js-safe-area-bottom', `${this.insets.bottom}px`
    );
    
    // 동적 클래스 토글
    document.body.classList.toggle('has-top-inset', this.insets.top > 0);
    document.body.classList.toggle('has-bottom-inset', this.insets.bottom > 0);
  }
  
  dispatchUpdateEvent() {
    window.dispatchEvent(new CustomEvent('safeAreaUpdate', {
      detail: this.insets
    }));
  }
  
  // 특정 요소에 안전 영역 적용
  applySafeArea(element, sides = ['top', 'right', 'bottom', 'left']) {
    sides.forEach(side => {
      const property = `padding-${side}`;
      const currentPadding = parseInt(getComputedStyle(element)[property]) || 0;
      const safeInset = this.insets[side];
      element.style[property] = `${Math.max(currentPadding, safeInset)}px`;
    });
  }
}

// 사용 예시
const safeAreaManager = new SafeAreaManager();

// 이벤트 리스너
window.addEventListener('safeAreaUpdate', (event) => {
  console.log('Safe area updated:', event.detail);
  
  // 커스텀 로직 실행
  updateAppLayout(event.detail);
});
```

---

## PWA와 env() 활용

### App Manifest와 연계
```json
{
  "name": "My PWA App",
  "display": "standalone",
  "theme_color": "#007AFF",
  "background_color": "#ffffff",
  "viewport": "width=device-width, initial-scale=1, viewport-fit=cover"
}
```

```css
/* PWA 모드에서의 안전 영역 처리 */
@media (display-mode: standalone) {
  .pwa-header {
    /* 상태바 영역 고려 */
    padding-top: max(env(safe-area-inset-top), 20px);
    background: var(--theme-color);
  }
  
  .pwa-content {
    /* 네이티브 앱처럼 전체 화면 활용 */
    height: calc(100vh - var(--header-height) - env(safe-area-inset-bottom));
  }
}

/* 브라우저 모드 */
@media (display-mode: browser) {
  .pwa-header {
    /* 브라우저 UI가 있으므로 최소 패딩만 */
    padding-top: 12px;
  }
}
```

---

## 성능 최적화 고려사항

### CSS 변수 캐싱
```css
/* 자주 사용하는 계산 결과를 CSS 변수로 캐싱 */
:root {
  --safe-area-top: env(safe-area-inset-top, 0);
  --safe-area-bottom: env(safe-area-inset-bottom, 0);
  
  /* 미리 계산된 조합값들 */
  --header-total-height: calc(60px + var(--safe-area-top));
  --content-available-height: calc(100vh - var(--header-total-height) - var(--safe-area-bottom));
  --min-padding: max(16px, var(--safe-area-top));
}

/* 매번 계산하지 않고 캐싱된 값 사용 */
.optimized-layout {
  height: var(--content-available-height);
  padding-top: var(--min-padding);
}
```

### 레이아웃 쓰래싱 방지
```css
/* 잘못된 예: 매번 다시 계산 */
.inefficient {
  height: calc(100vh - env(safe-area-inset-top) - env(safe-area-inset-bottom) - 60px);
  padding: env(safe-area-inset-top) 16px env(safe-area-inset-bottom);
  margin-top: env(safe-area-inset-top);
}

/* 올바른 예: 변수 활용으로 계산 최소화 */
.efficient {
  height: var(--content-height);
  padding: var(--safe-area-top) 16px var(--safe-area-bottom);
  margin-top: var(--safe-area-top);
}
```

---

## 디버깅 및 개발 도구

### 개발용 시각화
```css
/* 개발 모드에서 안전 영역 시각화 */
[data-debug="true"] {
  position: relative;
}

[data-debug="true"]::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  
  /* 안전 영역을 반투명 색상으로 표시 */
  border-top: env(safe-area-inset-top) solid rgba(255, 0, 0, 0.3);
  border-right: env(safe-area-inset-right) solid rgba(0, 255, 0, 0.3);
  border-bottom: env(safe-area-inset-bottom) solid rgba(0, 0, 255, 0.3);
  border-left: env(safe-area-inset-left) solid rgba(255, 255, 0, 0.3);
  
  pointer-events: none;
  z-index: 9999;
}

/* 개발용 정보 표시 */
.debug-info::after {
  content: 'Top: ' env(safe-area-inset-top) 
           ' Right: ' env(safe-area-inset-right)
           ' Bottom: ' env(safe-area-inset-bottom)
           ' Left: ' env(safe-area-inset-left);
  position: fixed;
  top: 10px;
  left: 10px;
  background: rgba(0, 0, 0, 0.8);
  color: white;
  padding: 8px;
  font-size: 12px;
  font-family: monospace;
  z-index: 10000;
}
```

### 브라우저 개발자 도구 활용
```javascript
// 콘솔에서 안전 영역 값 확인
console.log('Safe Area Insets:', {
  top: getComputedStyle(document.documentElement).getPropertyValue('env(safe-area-inset-top)'),
  right: getComputedStyle(document.documentElement).getPropertyValue('env(safe-area-inset-right)'),
  bottom: getComputedStyle(document.documentElement).getPropertyValue('env(safe-area-inset-bottom)'),
  left: getComputedStyle(document.documentElement).getPropertyValue('env(safe-area-inset-left)')
});

// 실시간 모니터링
function monitorSafeArea() {
  const observer = new ResizeObserver(() => {
    console.log('Safe area changed:', getSafeAreaInsets());
  });
  observer.observe(document.body);
}
```

---

## 관련 CSS 기능들

### aspect-ratio와 조합
```css
.video-container {
  aspect-ratio: 16 / 9;
  width: calc(100vw - env(safe-area-inset-left) - env(safe-area-inset-right));
  margin: 0 auto;
  padding: env(safe-area-inset-top) 0 env(safe-area-inset-bottom);
}
```

### scroll-padding과 조합
```css
.scroll-container {
  scroll-padding-top: calc(var(--header-height) + env(safe-area-inset-top));
  scroll-padding-bottom: calc(var(--tab-height) + env(safe-area-inset-bottom));
}
```

### overscroll-behavior와 조합
```css
.pull-to-refresh {
  overscroll-behavior-y: contain;
  padding-top: env(safe-area-inset-top);
}
```

---

# 💻 종합 예제
<details>
  <summary>code</summary>
  
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <title>CSS env() 종합 예제</title>
  <style>
    :root {
      /* 디자인 토큰 */
      --primary-color: #007AFF;
      --background-color: #f8f9fa;
      --text-color: #1d1d1f;
      --border-color: #d2d2d7;
      
      /* 안전 영역 변수 */
      --safe-top: env(safe-area-inset-top, 0);
      --safe-right: env(safe-area-inset-right, 0);
      --safe-bottom: env(safe-area-inset-bottom, 0);
      --safe-left: env(safe-area-inset-left, 0);
      
      /* 계산된 값들 */
      --header-height: 60px;
      --tab-height: 70px;
      --total-header-height: calc(var(--header-height) + var(--safe-top));
      --total-tab-height: calc(var(--tab-height) + var(--safe-bottom));
      --content-height: calc(100vh - var(--total-header-height) - var(--total-tab-height));
    }
    
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: var(--background-color);
      color: var(--text-color);
    }
    
    .app-container {
      display: grid;
      grid-template-rows: var(--total-header-height) 1fr var(--total-tab-height);
      height: 100vh;
    }
    
    .header {
      background: var(--primary-color);
      color: white;
      padding-top: var(--safe-top);
      padding-left: max(16px, var(--safe-left));
      padding-right: max(16px, var(--safe-right));
      display: flex;
      align-items: center;
      position: relative;
      z-index: 100;
    }
    
    .header-content {
      height: var(--header-height);
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    
    .main-content {
      overflow-y: auto;
      padding: 20px max(16px, var(--safe-left)) 20px max(16px, var(--safe-right));
    }
    
    .card {
      background: white;
      border-radius: 12px;
      padding: 20px;
      margin-bottom: 16px;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }
    
    .bottom-tabs {
      background: white;
      border-top: 1px solid var(--border-color);
      padding-bottom: var(--safe-bottom);
      padding-left: max(16px, var(--safe-left));
      padding-right: max(16px, var(--safe-right));
      display: flex;
      align-items: center;
    }
    
    .tabs-content {
      height: var(--tab-height);
      width: 100%;
      display: flex;
      justify-content: space-around;
      align-items: center;
    }
    
    .tab-item {
      flex: 1;
      text-align: center;
      padding: 8px;
      text-decoration: none;
      color: var(--text-color);
      border-radius: 8px;
      transition: background-color 0.2s;
    }
    
    .tab-item:hover {
      background: var(--background-color);
    }
    
    .fab {
      position: fixed;
      bottom: calc(20px + var(--total-tab-height));
      right: max(20px, calc(20px + var(--safe-right)));
      width: 56px;
      height: 56px;
      background: var(--primary-color);
      border: none;
      border-radius: 50%;
      color: white;
      font-size: 24px;
      cursor: pointer;
      box-shadow: 0 4px 12px rgba(0, 122, 255, 0.3);
      z-index: 1000;
    }
    
    /* 디버그 모드 */
    .debug-overlay {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      pointer-events: none;
      z-index: 9999;
      
      border-top: var(--safe-top) solid rgba(255, 0, 0, 0.2);
      border-right: var(--safe-right) solid rgba(0, 255, 0, 0.2);
      border-bottom: var(--safe-bottom) solid rgba(0, 0, 255, 0.2);
      border-left: var(--safe-left) solid rgba(255, 255, 0, 0.2);
    }
    
    .debug-info {
      position: fixed;
      top: calc(10px + var(--safe-top));
      left: calc(10px + var(--safe-left));
      background: rgba(0, 0, 0, 0.8);
      color: white;
      padding: 8px 12px;
      border-radius: 6px;
      font-family: monospace;
      font-size: 12px;
      z-index: 10000;
    }
    
    /* 반응형 조정 */
    @media (orientation: landscape) {
      .header {
        padding-left: max(20px, var(--safe-left));
        padding-right: max(20px, var(--safe-right));
      }
    }
    
    @media (min-width: 768px) {
      .app-container {
        max-width: 768px;
        margin: 0 auto;
      }
      
      .fab {
        display: none; /* 태블릿에서는 FAB 숨김 */
      }
    }
  </style>
</head>
<body>
  <div class="app-container">
    <header class="header">
      <div class="header-content">
        <h1>CSS env() Demo</h1>
        <button onclick="toggleDebug()">Debug</button>
      </div>
    </header>
    
    <main class="main-content">
      <div class="card">
        <h2>Safe Area Insets</h2>
        <p>이 앱은 CSS env() 함수를 사용하여 안전 영역을 자동으로 처리합니다.</p>
      </div>
      
      <div class="card">
        <h2>Dynamic Layout</h2>
        <p>디바이스 회전이나 화면 크기 변경 시에도 레이아웃이 자동 조정됩니다.</p>
      </div>
      
      <div class="card">
        <h2>Cross-Platform</h2>
        <p>iPhone, Android 등 다양한 디바이스에서 일관된 사용자 경험을 제공합니다.</p>
      </div>
    </main>
    
    <nav class="bottom-tabs">
      <div class="tabs-content">
        <a href="#" class="tab-item">홈</a>
        <a href="#" class="tab-item">검색</a>
        <a href="#" class="tab-item">알림</a>
        <a href="#" class="tab-item">프로필</a>
      </div>
    </nav>
  </div>
  
  <button class="fab">+</button>
  
  <!-- 디버그 오버레이 (기본 숨김) -->
  <div class="debug-overlay" style="display: none;"></div>
  <div class="debug-info" style="display: none;">
    Top: <span id="debug-top">0px</span><br>
    Right: <span id="debug-right">0px</span><br>
    Bottom: <span id="debug-bottom">0px</span><br>
    Left: <span id="debug-left">0px</span>
  </div>
  
  <script>
    let debugMode = false;
    
    function toggleDebug() {
      debugMode = !debugMode;
      const overlay = document.querySelector('.debug-overlay');
      const info = document.querySelector('.debug-info');
      
      if (debugMode) {
        overlay.style.display = 'block';
        info.style.display = 'block';
        updateDebugInfo();
      } else {
        overlay.style.display = 'none';
        info.style.display = 'none';
      }
    }
    
    function updateDebugInfo() {
      if (!debugMode) return;
      
      const style = getComputedStyle(document.documentElement);
      document.getElementById('debug-top').textContent = 
        style.getPropertyValue('--safe-top') || '0px';
      document.getElementById('debug-right').textContent = 
        style.getPropertyValue('--safe-right') || '0px';
      document.getElementById('debug-bottom').textContent = 
        style.getPropertyValue('--safe-bottom') || '0px';
      document.getElementById('debug-left').textContent = 
        style.getPropertyValue('--safe-left') || '0px';
    }
    
    // 방향 변경 및 리사이즈 시 디버그 정보 업데이트
    window.addEventListener('orientationchange', () => {
      setTimeout(updateDebugInfo, 100);
    });
    
    window.addEventListener('resize', updateDebugInfo);
  </script>
</body>
</html>
```
</details>

<br />

# 🔗 References
- CSS Environment Variables Module Level 1: https://drafts.csswg.org/css-env-1/
- MDN - env(): https://developer.mozilla.org/en-US/docs/Web/CSS/env
- WebKit Blog - Designing Websites for iPhone X: https://webkit.org/blog/7929/designing-websites-for-iphone-x/
- CSS Working Group - Safe area discussion: https://github.com/w3c/csswg-drafts/issues/2817
