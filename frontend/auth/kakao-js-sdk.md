> 📅 Date: 2025-04-29

# 📌 Focus
- 카카오 로그인 (JavaScript SDK)

<br />

# 📝 Learnings

## ✅ JavaScript SDK란?

> 웹 환경에서 카카오 API를 쉽게 사용할 수 있도록 제공하는 JavaScript 전용 라이브러리

- 브라우저에서 직접 카카오 로그인 및 기타 API 호출 가능
- 별도 서버 없이 프론트엔드 단독으로 로그인 연동 가능

<br />

## ✅ 카카오 로그인 과정 (JavaScript SDK)

**1단계. 애플리케이션 등록**
- [Kakao Developers](https://developers.kakao.com/)에서 앱 생성
- 플랫폼 → 웹사이트 등록
- Redirect URI 설정 (ex: `https://yourdomain.com/oauth`)

**2단계. SDK 설치 및 초기화**

```html
<script src="https://developers.kakao.com/sdk/js/kakao.js"></script>

<script>
  Kakao.init('YOUR_APP_KEY'); // 앱 키로 초기화
  console.log(Kakao.isInitialized()); // true 확인
</script>
```

- **YOUR_APP_KEY**는 Kakao Developers 콘솔의 JavaScript 키 사용

**3단계. 로그인 요청**

```javascript
Kakao.Auth.authorize({
  redirectUri: 'https://yourdomain.com/oauth'
});
```
- 사용자는 카카오 로그인 화면으로 리다이렉트
- 로그인 후 등록된 `redirectUri`로 돌아옴 (쿼리스트링에 인가 코드 포함)

**4단계. 토큰 발급 및 사용자 정보 요청**

로그인 완료 후 `Kakao.Auth.getAccessToken()`으로 액세스 토큰 획득 후:

```javascript
Kakao.API.request({
  url: '/v2/user/me'
})
.then(function(response) {
  console.log(response);
})
.catch(function(error) {
  console.error(error);
});
```
- 사용자 정보(프로필, 이메일 등) 가져오기 가능

<br />

## ✅ 앱 등록 및 권한 설정 주의사항

| 항목 | 설명 |
|------|------|
| 플랫폼 등록 필수 | 웹사이트 URL 정확히 등록 |
| Redirect URI 등록 필수 | 로그인 리다이렉트 성공을 위해 필요 |
| 앱 키 관리 주의 | JavaScript 키를 외부에 노출하지 않도록 주의 |
| 권한 관리 | 이메일, 프로필은 기본 제공, 추가 정보(전화번호 등)는 검수 및 권한 신청 필요 |
| 비즈 앱 전환 권장 | 카카오싱크 사용, 추가 개인정보 제공 가능

<br />

## ✅ 카카오 로그인 장점

| 항목 | 설명 |
|------|------|
| 빠른 연동 | SDK 스크립트만 삽입하면 바로 사용 가능 |
| 카카오 사용자 기반 활용 | 국내 사용자 수가 압도적으로 많음 |
| 다양한 연동 기능 지원 | 로그인 외에도 카카오톡 공유, 채널 추가 가능 |
| 카카오싱크와 연계 | 추가 회원가입 없이 간편 로그인 가능 |
| 모바일 최적화 | 모바일 웹에서도 매우 자연스럽게 동작

<br />

## ✨ 요약 (JavaScript SDK 기반 로그인 프로세스)

1. Kakao Developers에서 앱 등록 및 플랫폼/Redirect URI 설정
2. 웹에 kakao.js 스크립트 삽입 및 Kakao.init 실행
3. Kakao.Auth.authorize로 로그인 요청
4. 로그인 성공 시 액세스 토큰 획득 및 사용자 정보 조회

<br />

# 🔗 References
- [카카오 JavaScript SDK 시작하기 공식 문서](https://developers.kakao.com/docs/latest/ko/javascript/getting-started)
- [카카오 로그인 가이드](https://developers.kakao.com/docs/latest/ko/kakaologin/js)
