> 📅 Date: 2025-04-30

# 📌 Focus  
- Firebase Custom Token: 보안 및 리프레시 전략

<br />

# 📝 Learnings

## ✅ Custom Token 특성 요약

| 항목 | 설명 |
|------|------|
| 생성 주체 | Firebase Admin SDK (백엔드에서 생성) |
| 유효 시간 | 기본적으로 **1시간** 동안 유효 |
| 사용 시점 | **한 번만 사용 가능** (로그인 즉시 소모) |
| refresh_token 포함 여부 | ❌ 직접 발급한 커스텀 토큰에는 refresh_token이 없음

<br />

## ✅ 보안 전략

### 1. Custom Token 노출 방지

- Custom Token은 **로그인 1회용 토큰**
- 네트워크로 전달될 때 HTTPS 사용 필수
- JS에서 localStorage에 저장 ❌ → **로그인 후 즉시 사용 후 삭제**

<br />

### 2. 백엔드 인증 절차 검증

- 클라이언트에서 받은 `code`가 유효한지 반드시 **state 값으로 검증**
- code → access_token → 사용자 정보 요청 과정 모두 **토큰 유효성 검사 필수**

<br />

### 3. Firebase UID 관리

- 사용자 UID는 `kakao:123456`, `naver:abcdefg` 형태로 prefix를 붙여 중복 방지
- 이 UID로 Firebase 사용자 DB를 관리하고 **중복 로그인 방지**

<br />

### 4. Firebase 보안 규칙 설정

- Firebase Auth에 로그인된 사용자만 데이터 접근 가능하도록 rule 설정

```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null"
  }
}
```

---

## ✅ 리프레시 전략

### ❗ 문제: Firebase Custom Token은 refresh_token이 없음

- Firebase 자체 로그인(Google 등)은 refresh_token이 자동 갱신됨
- Custom Token은 사용자가 **로그인 다시 시도**해야 갱신 가능

<br />

### 🔄 해결 전략

| 전략 | 설명 |
|------|------|
| 1. 백엔드에서 재로그인 API 제공 | access_token 만료 시, 프론트가 다시 code → backend → custom token 재요청 |
| 2. 사용자 세션 유지 | 자체 세션 (ex. JWT in cookie)로 로그인 상태 유지, 필요 시 자동 재로그인 |
| 3. access_token 저장 주의 | 민감 데이터는 Secure + HttpOnly Cookie 활용 권장

<br />

### ✅ 자동 재인증 흐름 예시

```tsx
// Firebase 세션이 만료됐을 때
onAuthStateChanged(auth, (user) => {
  if (!user) {
    // 백엔드 API 요청 → 새로운 custom token 발급
    const newToken = await axios.get('/api/auth/kakao/refresh');
    await signInWithCustomToken(auth, newToken);
  }
});
```

- 이 방식으로 사용자 로그아웃을 막고 자동으로 갱신 가능

<br />

## ✅ 요약

- Custom Token은 로그인 1회성으로 사용 → **refresh_token 없음**
- 재인증 로직은 백엔드에서 제공하거나 자체 세션 유지 방식 설계 필요
- 사용자 UID 및 세션 관리, 보안 규칙 설정으로 전체 보안 강화 필수

<br />

# 🔗 References
- [Firebase Custom Auth 문서](https://firebase.google.com/docs/auth/web/custom-auth)
- [JWT + Firebase 인증 아키텍처 사례](https://firebase.google.com/docs/auth/admin/create-custom-tokens)
