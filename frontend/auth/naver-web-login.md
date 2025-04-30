> 📅 Date: 2025-04-30

# 📌 Focus
- 네이버 로그인 구현 방법 (OAuth 2.0 기반)

<br />

# 📝 Learnings

## ✅ 네이버 로그인

> 네이버 아이디로 로그인(Naver Login)은 OAuth 2.0 프로토콜을 기반으로,  
> 네이버 계정을 사용해 외부 앱/서비스에서 로그인할 수 있도록 제공하는 인증 시스템

<br />

## ✅ 네이버 로그인 구현 흐름

### 1단계. 애플리케이션 등록

- [네이버 개발자센터](https://developers.naver.com/main/) → 애플리케이션 등록
- **필수 입력 항목**:
  - 애플리케이션 이름
  - 서비스 환경 (웹 서비스 URL 등)
  - 로그인 Redirect URI 등록
- 클라이언트 ID와 시크릿 키 발급

<br />

### 2단계. 로그인 URL 생성

```http
https://nid.naver.com/oauth2.0/authorize?
  response_type=code
  &client_id=YOUR_CLIENT_ID
  &redirect_uri=YOUR_CALLBACK_URL
  &state=RANDOM_STRING
```

- `response_type=code` 고정
- `client_id`: 네이버에서 발급받은 앱의 ID
- `redirect_uri`: 로그인 완료 후 사용자를 리디렉션할 URI
- `state`: CSRF 방지를 위한 난수

<br />

### 3단계. 인가 코드 수신

- 사용자가 네이버 로그인 후 동의하면 `redirect_uri`로 리다이렉션됨
- 쿼리 파라미터로 `code`와 `state`가 함께 전달됨

```http
GET https://yourapp.com/callback?code=abc123&state=randomString
```

<br />

### 4단계. 액세스 토큰 요청 (백엔드 처리)

```http
POST https://nid.naver.com/oauth2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=YOUR_CLIENT_ID
&client_secret=YOUR_CLIENT_SECRET
&code=RECEIVED_CODE
&state=RECEIVED_STATE
```

- 성공 시 `access_token`, `refresh_token`, `token_type`, `expires_in` 반환

<br />

### 5단계. 사용자 정보 요청

```http
GET https://openapi.naver.com/v1/nid/me
Authorization: Bearer {access_token}
```

- 사용자 고유 ID, 이름, 이메일 등 기본 정보 반환

<br />

## ✅ 프론트엔드 + 백엔드 분리 구조에서의 흐름

1. **프론트**: 로그인 요청 → `code`, `state` 수신
2. **프론트 → 백엔드**: code 전달
3. **백엔드**:
   - 토큰 요청
   - 사용자 정보 요청
   - 자체 회원 처리 (가입 or 로그인)
   - JWT 발급 후 프론트에 응답
4. **프론트**: JWT 저장 및 로그인 완료 처리

<br />

## ✅ 네이버 로그인 장점

| 항목 | 설명 |
|------|------|
| 국내 사용자 기반 확보 | 많은 사용자가 네이버 계정 보유 |
| OAuth 표준 기반 | 타 소셜 로그인과 구현 구조 유사 |
| 추가 정보 제공 가능 | 성별, 생일, 나이대 등 요청 가능 (권한 설정 필요) |
| REST API 방식 지원 | 모바일/웹 어디서나 사용 가능 |

<br />

## ✅ 구현 시 주의사항

| 주의 항목 | 설명 |
|-----------|------|
| Redirect URI 정확 등록 | 네이버에 등록한 것과 정확히 일치해야 함 |
| state 값 검증 필수 | CSRF 방지를 위해 필수 보안 조치 |
| 사용자 정보 요청 시 헤더 필수 | `Authorization: Bearer` 형식 명확히 설정 |
| 클라이언트 시크릿 보안 | 백엔드에서만 사용, 프론트 노출 금지 |

<br />

# 🔗 References
- [네이버 개발자센터](https://developers.naver.com/)
- [OAuth 2.0 인증 구조 설명 (RFC 6749)](https://datatracker.ietf.org/doc/html/rfc6749)
