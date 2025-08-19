> 📅 Date: 2025-08-19

# 📌 Focus
- 모바일(특히 iOS Safari)에서 작은 폰트 크기 때문에 입력창 포커스 시 화면이 자동 확대
- UX 대응 방법

<br />

# 📝 Learnings

## 폰트 사이즈가 작으면 자동 확대
- iOS Safari는 가독성을 위해 input/select/textarea의 글자 크기가 작으면(일반적으로 16px 미만) 포커스 시 자동으로 화면을 확대(zoom-in)함.
- 크롬 환경도 마찬가지
- 결과적으로
  - 화면이 갑자기 확대되고 레이아웃이 흔들림
  - 고정 헤더/푸터, sticky 요소가 틀어져 보임
  - blur 이후에도 스케일이 원복되지 않거나 어색한 전환 발생

---

## 그렇게 되는 이유는?
- 접근성 차원에서 “작은 텍스트 입력”은 확대가 필요하다고 간주.
- 주요 트리거
  - form control(font-size < 16px)
  - 적절한 viewport meta가 없음
- 플랫폼별 경향
  - iOS Safari/Chrome on iOS: 자동 확대 이슈가 흔함
  - Android Chrome: viewport 설정이 올바르면 자동 확대 빈도 낮음

---

## 해결 방법 
- 입력 요소의 글꼴 크기를 최소 16px 이상으로 유지
- 올바른 viewport 설정 유지
- 디자인 시스템에서 rem 기반 크기 체계를 사용해 일관성 확보

```html
<!-- Viewport는 이렇게 (접근성 차원에서 user-scalable 제한은 지양) -->
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

```css
/* 모든 폼 컨트롤의 최소 글자 크기를 16px 이상 보장 */
input, select, textarea, button {
  font-size: 16px; /* 또는 1rem (html { font-size: 16px; }를 전제로) */
  line-height: 1.4;
}

/* 글로벌 타입 스케일 예시 */
html { font-size: 16px; }      /* 기준 16px */
body { font-size: 1rem; }      /* 16px */
.small { font-size: 0.875rem;} /* 14px (폼 컨트롤엔 적용 X 권장) */
```

- user-scalable=no, maximum-scale=1 같은 제한은 접근성에 악영향 → 가급적 피함.
- -webkit-text-size-adjust는 텍스트 자동 확대에 관여하지만, 무분별한 값(예: none)은 접근성 저하 가능 → 기본(100%) 유지 권장.

---

## 대안/우회(Hack) – 꼭 필요할 때만
- 시각적인 “작아 보임”만 필요하고 자동 확대는 막고 싶다면:
  - 실제 font-size는 16px로 두고, transform으로 축소
  - 주의: caret 위치, 클릭 영역, 터치 타깃에 영향 가능. 충분한 QA 필요.

```css
/* iOS에서만 적용하고 싶을 때의 한 예시 */
@supports (-webkit-touch-callout: none) {
  .ios-shrink {
    font-size: 16px;
    transform: scale(0.875);      /* 시각적으로 14px처럼 보이게 */
    transform-origin: left center; /* 커서/레이블 어긋남 최소화 */
  }
}
```

- 특정 input에만 적용하고 싶다면 해당 클래스에만 ios-shrink를 부여.
- wrapper에 scale을 주고 내부 padding/height를 조정하는 방식도 있으나, 터치 타깃/정렬 이슈가 생길 수 있음.

---

## 자주 겪는 함정
- CSS reset/컴포넌트 라이브러리가 폼 컨트롤에 14px 같은 값을 기본으로 지정 → iOS에서 포커스 시 확대
- 작은 placehoder, helper text는 OK라도, 실제 입력 필드의 font-size는 16px 이상이어야 함
- 숫자 키패드(type="number" 등)만 떠도 확대될 수 있음 → font-size로 방어
- WKWebView(하이브리드 앱)에서도 동일 현상 재현 가능

---

## 테스트 체크리스트
- 실제 iPhone Safari/Chrome에서 input, select, textarea 모두 포커스 테스트
- 가로/세로 회전 모두 확인
- 접근성 설정(글자 크게 보기) 활성화 상태에서도 레이아웃/스크롤 이슈 없는지 확인
- sticky/fixed 헤더/푸터가 포커스 시 흔들리지 않는지
- blur 후 스케일이 자연스럽게 유지/복원되는지

---

## 최소 재현/해결 예시

```html
<!doctype html>
<html lang="ko">
  <head>
    <meta charset="utf-8">
    <meta name="viewport"
          content="width=device-width, initial-scale=1, viewport-fit=cover">
    <title>iOS Auto Zoom Demo</title>
    <style>
      html { font-size: 16px; }
      body { margin: 0; font-family: system-ui, -apple-system, Segoe UI, Roboto, sans-serif; }
      header { position: sticky; top: 0; padding: 12px 16px; background: #111; color: #fff; }
      main { padding: 16px; }
      /* 해결안: 폼 컨트롤은 최소 16px 이상 */
      input, select, textarea {
        font-size: 16px;
        padding: 10px 12px;
        border: 1px solid #ccc;
        border-radius: 8px;
      }
      .row { margin: 12px 0; }
    </style>
  </head>
  <body>
    <header>Sticky Header</header>
    <main>
      <div class="row">
        <label>정상(16px): </label>
        <input placeholder="포커스해도 확대되지 않음" />
      </div>
      <div class="row">
        <label>문제(12px): </label>
        <input style="font-size:12px" placeholder="iOS에서 확대될 수 있음" />
      </div>
    </main>
  </body>
</html>
```

---

# 🔗 References
- MDN: meta viewport 가이드 — https://developer.mozilla.org/docs/Web/HTML/Viewport_meta_tag
- WebKit 블로그(텍스트 크기/뷰포트 관련 참고) — https://webkit.org/blog/
- Smashing Magazine: Prevent iOS input zoom — https://www.smashingmagazine.com/2013/01/ios-forms-iphone-5/
- CSS-Tricks: iOS input zooming — https://css-tricks.com/16px-or-larger-text-prevents-ios-form-zoom/
