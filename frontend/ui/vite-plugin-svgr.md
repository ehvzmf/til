> 📅 Date: 2025-05-21

# 📌 Focus

**Vite + vite-plugin-svgr** <br />
(svg 파일 컬러 변경을 위해 사용)

<br />

# 📝 Learnings
> `.svg` 파일을 **React 컴포넌트처럼 JSX로 직접 사용**할 수 있게 해주는 Vite 플러그인

```tsx
import { ReactComponent as Logo } from './logo.svg';

<Logo width={100} height={100} />;
```

* 일반 이미지처럼 `<img src={logo} />` 대신 JSX로 사용할 수 있어
* **스타일 조정, 색상 변경, 애니메이션 처리 등 React의 장점을 그대로 활용 가능**

```bash
pnpm add -D vite-plugin-svgr
```

<br />

## ✅ Vite 설정에 적용

### `vite.config.ts`

```ts
import svgr from 'vite-plugin-svgr';
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    react(),
    svgr(), // ✅ svgr 플러그인 추가
  ],
});
```

<br />

## ✅ 사용법

SVG 파일을 불러올 때 두 가지 방식 중 선택 가능:

### 1️⃣ 이미지 URL로 사용 (기본)

```tsx
import logoUrl from './logo.svg';

<img src={logoUrl} alt="logo" />;
```

### 2️⃣ React 컴포넌트로 사용 (SVGR 방식)

```tsx
import { ReactComponent as Logo } from './logo.svg';

<Logo width={120} height={120} />;
```

✅ 이 기능을 사용하려면 **`ReactComponent`로 import**해야 함

<br />

## ✅ TS 지원을 위한 타입 선언 추가

```ts
// src/custom.d.ts (또는 types/svg.d.ts 등)

declare module '*.svg?react' {
  import * as React from 'react';
  const ReactComponent: React.FunctionComponent<
    React.SVGProps<SVGSVGElement> & { title?: string }
  >;
  export { ReactComponent };
}

declare module '*.svg' {
  const src: string;
  export default src;
}
```

---

## ✅ 옵션 설정 예시 (필요 시)

```ts
svgr({
  svgrOptions: {
    icon: true, // 뷰박스를 기준으로 자동 크기 조정
  },
});
```

---

## ✅ Usage

| 항목            | 설명                                            |
| ------------- | --------------------------------------------- |
| 아이콘처럼 쓰고 싶을 때 | `icon: true` 옵션 활용하면 width/height 자동 조절됨      |
| 색상 변경         | `fill="currentColor"` 속성이 있는 경우 CSS로 색상 제어 가능 |
| 애니메이션         | `className`으로 Tailwind 등 애니메이션 클래스 추가 가능      |
| 접근성           | `role="img"`, `aria-label` 등 필요한 경우 속성 추가 가능  |

---

## ✅ Caution

| 항목                 | 설명                                                        |
| ------------------ | --------------------------------------------------------- |
| SVG 내부 fill 고정값 제거 | `fill="#000"`처럼 하드코딩된 색은 제거해야 `fill="currentColor"` 적용 가능 |
| 경로 import 충돌       | 같은 SVG를 이미지로도 쓰는 경우 파일명을 분리하거나 별칭 사용 고려                   |
