> 📆 2025-04-29

# 📌 Focus
- 프론트엔드/백엔드 분리 환경에서 카카오 로그인 처리 (REST API)

<br />

# 🪼 실제 구현 과정

1. Kakao Developers에서 앱 등록 및 키 발급 (백이 함) 
2. `.env`에 REST_API_KEY, REDIRECT_URI 저장
3. 해당 URL로 이동
```ts
const handleKakaoLogin = () => {
  const kakaoAuthUrl = `https://kauth.kakao.com/oauth/authorize?response_type=code&client_id=${import.meta.env.VITE_KAKAO_REST_API_KEY}&redirect_uri=${import.meta.env.VITE_REDIRECT_URI}`;
  window.location.href = kakaoAuthUrl;
}
```
4. 클라이언트에서 로그인 요청

<br /> 

### 🔥 KOE101 오류 발생 !
REST_API_KEY 잘못 전달 받음 > 해결

<br />

5. 



---

# 📝 전체 플로우 정리

## ✅ 기본 구조

**프론트엔드 역할**  
- 사용자를 카카오 로그인 화면으로 리다이렉트
- 인가 코드(code)를 받아 백엔드로 전달

**백엔드 역할**  
- 인가 코드(code)를 가지고 카카오 서버에 access_token 요청
- access_token으로 사용자 정보 조회
- 자체 서비스용 로그인 처리 및 JWT 발급

<br />

## ✅ 세부 흐름

### 1단계. 프론트엔드: 카카오 로그인 요청

```tsx
// 예시: 로그인 버튼 클릭 시
const kakaoLogin = () => {
  const KAKAO_AUTH_URL = `https://kauth.kakao.com/oauth/authorize?client_id=${KAKAO_REST_API_KEY}&redirect_uri=${REDIRECT_URI}&response_type=code`;
  window.location.href = KAKAO_AUTH_URL;
};
```
- 사용자를 카카오 로그인 페이지로 리다이렉트

<br />

### 2단계. 프론트엔드: 인가 코드 수신

- 카카오 로그인 성공 후, `REDIRECT_URI`로 리다이렉트되며 URL에 `?code=xxxxx` 포함
- 프론트엔드는 이 **code**를 파싱

```tsx
const url = new URL(window.location.href);
const code = url.searchParams.get('code');
```

<br />

### 3단계. 프론트엔드 → 백엔드: 인가 코드 전송

- 프론트는 이 code를 **백엔드 API**로 POST 요청

```tsx
await axios.post('/api/auth/kakao', { code });
```

<br />

### 4단계. 백엔드: 토큰 요청 (카카오 서버)

- 백엔드는 전달받은 code를 사용해 카카오로 access_token 요청

```http
POST https://kauth.kakao.com/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
client_id={REST_API_KEY}
redirect_uri={REDIRECT_URI}
code={받은 인가 코드}
```

✅ 성공 시:
- `access_token`, `refresh_token` 응답 수신

<br />

### 5단계. 백엔드: 사용자 정보 조회

- 받은 access_token을 사용해 사용자 정보 요청

```http
GET https://kapi.kakao.com/v2/user/me
Authorization: Bearer {access_token}
```

✅ 사용자 기본 정보 (id, email, nickname 등) 획득

<br />

### 6단계. 백엔드: 자체 회원 시스템 연동

| 단계 | 설명 |
|------|------|
| 기존 회원인지 검사 | 이메일 또는 고유 id로 DB 조회 |
| 신규 회원 가입 처리 | 없으면 자동 가입 처리 가능 |
| JWT 토큰 발급 | 자체 access token 발급하여 프론트에 전달 |

<br />

### 7단계. 프론트엔드: JWT 저장 및 로그인 완료 처리

- 백엔드에서 받은 **자체 JWT**를 브라우저에 저장 (ex. localStorage, cookie)
- 이후 API 요청 시 Authorization 헤더에 JWT 첨부

```tsx
axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
```

<br />

## ✅ 요약: 프론트 + 백엔드 분리 시 카카오 로그인 흐름

1. 프론트 → 카카오 인가 요청
2. 카카오 → 프론트 리다이렉트 (code 전달)
3. 프론트 → 백엔드에 code 전달
4. 백엔드 → 카카오에 토큰 요청
5. 백엔드 → 사용자 정보 요청
6. 백엔드 → 자체 토큰 발급
7. 프론트 → 자체 토큰 저장 및 로그인 완료

<br />

## ✅ 주의할 점

| 주의사항 | 설명 |
|----------|------|
| redirect_uri 정확히 일치 | 카카오에 등록된 것과 요청하는 것 일치 필수 |
| client_secret 사용 권장 | 보안 강화 위해 설정 (선택) |
| code 노출 최소화 | 프론트에서는 code만 다루고 토큰 처리는 무조건 백엔드에서 |
| 사용자 정보 최소 수집 | 필요한 정보만 요청하고, 개인정보 보호 신경쓰기 |
| JWT 보안 관리 | 토큰 탈취 방지 위해 Secure, HttpOnly 설정 고려 |

<br />

# 🔗 References
- [카카오 REST API 로그인 문서](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)
- [OAuth 2.0 Authorization Code Flow](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1)
