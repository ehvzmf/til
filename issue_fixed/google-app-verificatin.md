> 📅 Date: 2025-09-04

# 📌 Focus
> Google App verification <br />
> 클라우드 콘솔에서 브랜딩 작성 후, (도메인/개인정보처리방침/약관 페이지 링크 추가) 인증을 위해 앱을 제출 <br />
> `not registered to you`... 라는 메일을 받음  <br />

<br />

# 📝 Learnings
**docs**
- [Verify your site ownership](https://support.google.com/webmasters/answer/9008080?hl=en)
- [Add a website property to Search Console](https://support.google.com/webmasters/answer/34592)

<br />

**Google Search Console**
> [Google Search Console](https://search.google.com/search-console?hl=ko)에서 도메인 소유권 확인 필요 <br />
OAuth 프로젝트와 같은 계정으로 로그인 → **속성 추가** → **URL 접두어** 방식 → URL 입력 <br />
Google Analytics의 gtag가 이미 설치되어 있으면 바로 확인 가능 <br />
⚠️ gtag나 확인 토큰은 삭제하면 안됨 (주기적 재확인) <br />

<br />

**Other methods**
- **HTML 태그**: `<meta name="google-site-verification" content="토큰" />` 추가
- **HTML 파일 업로드**: Google 제공 파일을 웹사이트 루트에 업로드
- **DNS TXT 레코드**: 도메인 DNS 설정에서 TXT 레코드 추가
- **Google Tag Manager**: 기존 GTM 코드 활용

<br />

**Key Points**
- 도메인 소유권 확인은 **Google Search Console**에서 진행 (Google Cloud Console 아님)
- 여러 확인 방법을 동시에 사용 가능 (백업용)
- 확인 토큰은 영구 유지 필요
- 데이터 수집은 확인 전부터 시작되지만 며칠 후부터 표시
  
<br />

# 🔗 References
- [Google Search Console](https://search.google.com/search-console)
- [Google Cloud Console - OAuth 설정](https://console.cloud.google.com)
- [Site ownership verification methods](https://support.google.com/webmasters/answer/9008080)
