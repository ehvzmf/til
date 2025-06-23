> 📅 Date: 2025-06-23

# 📌 Focus
- How to render HTML strings in React
- Sanitizer

# 📝 Learnings

> **React 앱에서 html 문자열 렌더링하기**
> (mui 기준)

```
<Box
  sx={{
    whiteSpace: 'pre-line',
    wordBreak: 'keep-all',
  }}
  dangerouslySetInnerHTML={{ __html: post.content }}
/>
```
- JSX에서 `{post.content}`처럼 문자열을 출력하면 HTML 태그가 텍스트로 보인다.
- 실제 HTML로 렌더링하려면 `dangerouslySetInnerHTML={{ __html: post.content }}`를 사용한다.
- 단, ⚠️ html을 그대로 띄우는 건 위험 ⚠️

<br />

## HTML 문자열을 그대로 쓰면 안 되는 이유

**1. XSS(크로스사이트 스크립팅) 공격 위험**

- 악의적인 사용자가 `<script>`, `<img onerror=...>`, `<iframe>` 등 각종 스크립트나 이벤트 속성을 삽입할 수 있다. 
- 쿠키 탈취, 사용자 세션 하이재킹, 사이트 변조, 피싱/악성코드 유포 등 보안사고 발생 가능

```html
<!-- 악성 사용자가 입력한 값 -->
Hello <script>alert('해킹!')</script>
```
-> 사용자 브라우저에서 `alert('해킹!')`이 실행됩니다.

<br />

**2. 사용자 신뢰 및 서비스 평판 하락**

XSS 취약점은 **OWASP Top 10**(웹 취약점 상위 10개) 중 하나로, 보안 감사를 받을 때 필수적으로 점검하는 항목

<br />

**3. 데이터 무결성/정상적인 UI 방해**

- 악성 HTML이 삽입되면 레이아웃 깨짐/페이지 기능 비정상 동작 위험
- 방치하면 사이트 전체 품질이 저하될 수 있다.

<br />

## Sanitizer

- 위와 같은 이유로, sanitizer를 사용한다.
- 입력된 HTML/JS/CSS 코드에서 위험한 부분(악성 스크립트 등)을 제거하는 필터(도구/라이브러리)
- XSS 공격 등에서 웹사이트를 보호할 수 있다.
- **Sanitizer(정화기)**를 사용해서
  - 허용된 태그/속성만 남기고  
  - 나머지(특히 스크립트, 위험 속성)는 제거
- `DOMPurify`, `sanitize-html`, `xss`, `js-xss`

### Sanitizer를 적용해야 하는 경우
- 사용자가 입력한 HTML/텍스트를 웹페이지에 표시할 때
- 게시글, 댓글 등 외부 입력이 화면에 노출될 때
- 이메일, 채팅, 마크다운 에디터 등에서 HTML을 렌더링할 때

<br />

## 4. 대표적인 sanitizer 라이브러리 비교
- **DOMPurify**  
  - 보안성, 속도, 유지보수성 모두 우수.  
  - React 등 프론트엔드에서 가장 많이 쓰이고 권장된다.
- **sanitize-html**  
  - 세밀한 허용 태그/속성 지정 가능, Node.js 환경에서 강점.
- **xss/js-xss**  
  - 매우 가볍고 빠르지만, 세밀한 정책 적용은 어려움.

<br />

## 5. React에서 DOMPurify 적용 방법
1. `pnpm install dompurify`로 설치
2. `import DOMPurify from 'dompurify'`로 불러오기
3. HTML을 렌더링할 때  
   `<Box dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(post.content) }} />`  
   형태로 사용

<br />

# 🔗 References
- [React에서 string 형태 HTML 태그 안전하게 렌더링하기](https://velog.io/@yejine2/react-html%ED%83%9C%EA%B7%B8-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EB%9E%9C%EB%8D%94%EB%A7%81)
- [sanitize-html](https://stack94.tistory.com/entry/%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-sanitize-html-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-%EB%B3%B4%EC%95%88%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0%ED%95%98%EC%9E%90#google_vignette)
