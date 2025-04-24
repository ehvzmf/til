> 📅 Date: 2025-04-24

# 📌 Focus
- 온보딩 모달 초기 렌더 지연 및 깜빡임 현상 해결 전략

<br />

# 📝 Learnings

## ❗ 문제 상황 요약

온보딩 후 바로 띄우는 **모달에서 다음과 같은 문제가 발생**:

- **모달 배경보다 텍스트가 먼저 나타남**
- 배경 이미지가 늦게 로딩되면서 **텍스트가 보이지 않거나 깜빡임**
- 전체적으로 **사용자 경험이 거칠고 부자연스러움**

---

## 🔎 원인 분석

| 현상 | 원인 |
|------|------|
| 텍스트 먼저 뜨고 배경 늦게 로딩 | `<img />`가 렌더될 때 실제 다운로드가 시작됨 |
| 화면에 깜빡임 발생 | 렌더 타이밍 불균형, 이미지 지연 로딩 |
| 폰트 깨짐 or 늦게 뜸 | web font의 비동기 로딩 (FOUT 현상) |

---

## ✅ 해결 전략 (3단계)

### ① 이미지 프리로드

```ts
useEffect(() => {
  const img = new Image();
  img.src = '/images/onboarding/hands_sm.svg';
}, []);
```

> **의도**: 이미지가 **모달 렌더 전에 미리 브라우저에 로딩**되어 깜빡임 감소  
> - `<img />`로 렌더되기 전에도 다운로드 진행됨  
> - 사용자 눈에는 모달 등장과 동시에 이미지가 자연스럽게 보임

---

### ② 콘텐츠 지연 렌더링

```ts
const [imageLoaded, setImageLoaded] = useState(false);

useEffect(() => {
  const img = new Image();
  img.src = '/images/onboarding/hands_sm.svg';
  img.onload = () => setImageLoaded(true);
}, []);
```

```tsx
<Modal open={open}>
  <Box>
    {imageLoaded ? (
      <모달 본문 내용 />
    ) : (
      <Box sx={{ width: 618, height: 582, backgroundColor: '#000' }} />
    )}
  </Box>
</Modal>
```

> **의도**: 이미지가 로딩되기 전에는 **빈 화면 또는 placeholder만 표시**  
> - `imageLoaded === true`일 때만 실제 콘텐츠 렌더  
> - 이미지 없을 때 화면이 깨져 보이는 문제 방지

---

### ③ Fade 전환 효과 (선택적)

```tsx
import { Fade } from '@mui/material';

<Modal open={open}>
  <Fade in={imageLoaded}>
    <Box>
      {/* 콘텐츠 */}
    </Box>
  </Fade>
</Modal>
```

> **의도**: 모달 콘텐츠가 **부드럽게 페이드 인**  
> - 화면이 툭 튀지 않고 자연스럽게 전환  
> - 이미지+텍스트 동시 등장 시 시각적 부드러움 증가

---

## ✨ 추가 팁: 폰트 프리로드

```html
<link rel="preload" href="https://your-font.woff" as="font" type="font/woff" crossorigin="anonymous" />
```

> **web font 로딩 지연(FOUT)**도 깜빡임 원인  
> - 텍스트가 **기본 시스템 폰트로 먼저 렌더**된 뒤  
> - 웹폰트 적용되며 깜빡이는 현상 발생  
> → preload 설정으로 **폰트 미리 다운로드 유도**

---

## 📌 요약 적용 순서

1. **이미지 미리 프리로드 (useEffect + Image 생성)**
2. **imageLoaded 체크 후 콘텐츠 렌더링**
3. **필요 시 Fade로 부드럽게 전환**
4. **폰트도 `preload` 태그로 미리 불러오기**

---

## 🧠 실무 인사이트

- **"사용자에게 보이는 순서"를 제어하는 것**도 프론트 UX의 핵심
- 성능 이슈가 아니더라도 **심리적 지연**이 사용자 이탈로 이어질 수 있음
- 이미지, 폰트 등 **외부 리소스의 렌더 타이밍**을 예측 가능하게 만드는 것이 중요

---

# 🔗 References
- [MDN - Image()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image)
- [Google Fonts - Preloading](https://web.dev/font-display/#ensure-text-is-visible)
