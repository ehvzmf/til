> 📅 Date: 2025-10-08

# 📌 Focus
- Tailwind CSS
<br />

# 📝 Learnings

## 1. Tailwind CSS란?
- Utility-first CSS 프레임워크
- 미리 정의된 클래스를 조합하여 스타일링
- 별도의 CSS 파일 작성 최소화

<br />

## 2. 설치 및 설정

### npm으로 설치
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init
```

### tailwind.config.js 설정
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
    "./public/index.html"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### CSS 파일에 Tailwind 추가
```css
/* index.css 또는 App.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

<br />

## 3. 기본 사용법

### 레이아웃
```jsx
// Flexbox
<div className="flex justify-center items-center">
  <p>가운데 정렬</p>
</div>

// Grid
<div className="grid grid-cols-3 gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

### 간격 (Spacing)
```jsx
// margin: m-{size}, mt-{size}, mr-{size}, mb-{size}, ml-{size}
// padding: p-{size}, pt-{size}, pr-{size}, pb-{size}, pl-{size}
// size: 0, 1(0.25rem), 2(0.5rem), 4(1rem), 8(2rem) 등

<div className="m-4 p-8">
  <p className="mt-2 mb-4">마진과 패딩 예제</p>
</div>
```

### 색상
```jsx
// 텍스트: text-{color}-{shade}
// 배경: bg-{color}-{shade}
// shade: 50, 100, 200, ... 900

<button className="bg-blue-500 text-white hover:bg-blue-700">
  버튼
</button>
```

### 타이포그래피
```jsx
<h1 className="text-4xl font-bold">제목</h1>
<p className="text-base text-gray-700 leading-relaxed">본문</p>
<span className="text-sm font-light italic">작은 텍스트</span>
```

### 크기
```jsx
// width: w-{size}, w-full, w-screen, w-1/2 등
// height: h-{size}, h-full, h-screen 등

<div className="w-full h-64 bg-gray-200">
  <img className="w-32 h-32" src="..." alt="..." />
</div>
```

<br />

## 4. 반응형 디자인
```jsx
// 브레이크포인트: sm(640px), md(768px), lg(1024px), xl(1280px), 2xl(1536px)
// 기본적으로 mobile-first (작은 화면부터 적용)

<div className="text-sm md:text-base lg:text-lg">
  반응형 텍스트
</div>

<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  {/* 모바일: 1열, 태블릿: 2열, 데스크탑: 4열 */}
</div>
```

<br />

## 5. 상태 변경 (Pseudo-classes)
```jsx
// hover, focus, active, disabled, first, last 등

<button className="bg-blue-500 hover:bg-blue-700 active:bg-blue-900 disabled:opacity-50">
  상호작용 버튼
</button>

<input className="border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-200" />

<ul>
  <li className="first:font-bold last:text-gray-400">리스트 항목</li>
</ul>
```

<br />

## 6. 커스텀 값 사용
```jsx
// 임의의 값: 대괄호 [] 사용

<div className="w-[350px] h-[calc(100vh-64px)] bg-[#1da1f2]">
  커스텀 크기와 색상
</div>

<p className="text-[14.5px] leading-[1.75]">커스텀 폰트</p>
```

<br />

## 7. Dark Mode
```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class', // 또는 'media'
  // ...
}
```

```jsx
<div className="bg-white dark:bg-gray-800 text-black dark:text-white">
  다크모드 지원
</div>
```

<br />

## 8. 자주 사용하는 유틸리티 조합

### 카드 컴포넌트
```jsx
<div className="max-w-sm rounded-lg overflow-hidden shadow-lg bg-white p-6">
  <h2 className="text-xl font-semibold mb-2">카드 제목</h2>
  <p className="text-gray-600">카드 내용</p>
</div>
```

### 중앙 정렬 컨테이너
```jsx
<div className="flex items-center justify-center min-h-screen">
  <div className="text-center">
    <h1>가운데 정렬된 콘텐츠</h1>
  </div>
</div>
```

### 입력 폼
```jsx
<input 
  type="text"
  className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
  placeholder="입력하세요"
/>
```

<br />

## 9. 성능 최적화

### PurgeCSS (자동 포함)
- 프로덕션 빌드 시 사용하지 않는 CSS 자동 제거
- `content` 배열에 올바른 경로 지정 필수

```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}",
  ],
  // ...
}
```

<br />

## 10. 유용한 팁

### @apply로 재사용 가능한 클래스 생성
```css
/* styles.css */
.btn-primary {
  @apply bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-700;
}
```

### 클래스 네이밍 조건부 적용 (with clsx 또는 classnames)
```jsx
import clsx from 'clsx';

<button className={clsx(
  'px-4 py-2 rounded',
  isPrimary ? 'bg-blue-500 text-white' : 'bg-gray-200 text-gray-800',
  isDisabled && 'opacity-50 cursor-not-allowed'
)}>
  버튼
</button>
```

### 테마 확장
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        'brand': '#1da1f2',
        'brand-dark': '#0d8bd9',
      },
      spacing: {
        '72': '18rem',
        '84': '21rem',
      },
      fontFamily: {
        'sans': ['Pretendard', 'sans-serif'],
      },
    },
  },
}
```

<br />

## 11. 주의사항
- 클래스 이름을 동적으로 생성하면 안 됨 (PurgeCSS가 인식 못함)
  ```jsx
  ❌ <div className={`text-${color}-500`}> // 작동 안 함
  ✅ <div className={color === 'red' ? 'text-red-500' : 'text-blue-500'}>
  ```
- 너무 많은 클래스 조합은 가독성 저하 → `@apply`나 컴포넌트 분리 고려
- VS Code Extension 설치 권장: "Tailwind CSS IntelliSense"

<br />

# 🔗 References
- [Tailwind CSS 공식 문서](https://tailwindcss.com/docs)
- [Tailwind CSS Playground](https://play.tailwindcss.com/)
- [Tailwind CSS Cheat Sheet](https://nerdcave.com/tailwind-cheat-sheet)
- [Headless UI (Tailwind와 궁합 좋은 컴포넌트)](https://headlessui.com/)
