> 📅 Date: 2025-04-29

# 📌 Focus
- KAKAO Login - REST API

<br />

# 📝 Learnings

## 🗨️ KAKAO Login
- 카카오 계정을 사용하여 앱/웹 서비스에 로그인할 수 있는 OAuth 2.0 기반 인증 시스템
- 빠른 소셜 로그인 구축
- REST API, JavaScript SDK, Android/iOS SDK 지원

<br />

## 🎢 Steps

**1. 앱 등록 및 설정**
- [Kakao Developers](https://developers.kakao.com/)에 회원가입 후, 애플리케이션 등록
- 애플리케이션 설정
  - 플랫폼 등록 (웹: 사이트 도메인, 모바일: 패키지명)
  - Redirect URI 등록 (OAuth 인증 완료 후 돌아올 URI)

**2. 인가 요청 (Authorization Request)**
- 사용자가 로그인하고 권한을 동의하는 단계
- 사용자가 카카오 로그인 화면을 통해 로그인/동의 → 인가 코드 발급
- 요청 예시:

```http
GET https://kauth.kakao.com/oauth/authorize
    ?client_id={REST_API_KEY}
    &redirect_uri={REDIRECT_URI}
    &response_type=code
```
- `client_id`: 내 애플리케이션의 REST API 키
- `redirect_uri`: 인가 완료 후 사용자를 리다이렉션할 URI
- `response_type=code` 고정

**3. 토큰 요청 (Token Request)**
- 인가 코드(code)를 받아서 access token 요청

```http
POST https://kauth.kakao.com/oauth/token
    Content-Type: application/x-www-form-urlencoded

    grant_type=authorization_code
    client_id={REST_API_KEY}
    redirect_uri={REDIRECT_URI}
    code={인가 코드}
```
- `grant_type=authorization_code` 고정
- 성공 시 `access_token`, `refresh_token` 반환

**4. 사용자 정보 요청**
- 발급받은 `access_token`으로 사용자 정보 요청

```http
GET https://kapi.kakao.com/v2/user/me
    Authorization: Bearer {access_token}
```

**5. (선택) 토큰 갱신**
- `refresh_token`을 사용해 access_token 갱신 가능

<br />

## ✅ 앱 등록 및 권한 설정 주의사항

- **플랫폼 설정**: 웹, Android, iOS 각각 따로 등록 필요
- **Redirect URI 정확히 등록**: 인증 과정 실패를 방지하기 위해 필요
- **필수 권한 설정**: 이메일, 프로필 등 기본 항목은 별도 설정 없이 제공
- **추가 권한 설정**: 카카오스토리, 카카오톡 채널 연동 등은 권한 신청 필요
- **비즈 앱 전환**: 카카오싱크 또는 사업용 기능 사용하려면 필수

<br />

## ✅ 카카오 로그인을 사용해야 하는 이유

| 항목 | 설명 |
|------|------|
| 높은 사용자 접근성 | 대한민국에서 카카오 사용자 수가 압도적으로 많음 |
| 빠른 인증 프로세스 | OAuth 2.0 표준을 따르면서도 직관적인 UX 제공 |
| 다양한 연동 기능 | 카카오톡 채널, 카카오스토리, 푸시 알림 등과 확장성 높음 |
| REST API 제공 | 다양한 클라이언트에서 손쉽게 연동 가능 |
| 카카오싱크 지원 | 추가 회원가입 절차 없이 간편 가입 가능 |

<br />

## ✨ 요약

1. Kakao Developers에서 애플리케이션 등록
2. Redirect URI 및 플랫폼 설정
3. 사용자에게 인가 코드 요청
4. 인가 코드로 토큰 발급 요청
5. 토큰을 사용해 사용자 정보 요청
6. (필요 시) refresh_token으로 access_token 갱신

<br />

# 🔗 References
- [카카오 로그인 REST API 공식 문서](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)
- [OAuth 2.0 표준 문서](https://datatracker.ietf.org/doc/html/rfc6749)
