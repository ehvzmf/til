> 📅 Date: 2025-04-30

# 📌 Focus  
- Firebase + 네이버/카카오 로그인 연동 (Custom Token)

<br />

# 📝 Learnings

## ✅ 개요

> Firebase는 기본적으로 Google, Facebook 등 주요 OAuth 제공자에 대한 로그인 연동을 지원하지만  <br />
> **네이버/카카오 같은 국내 OAuth 제공자와는 직접 연동이 불가능** <br />
> 이 경우, **Firebase Custom Token 방식**을 통해 연동

<br />

## ✅ 연동 흐름 요약

1. 사용자가 **카카오/네이버로 로그인** → access_token 획득
2. **백엔드에서 사용자 정보 확인**
3. 백엔드에서 Firebase Admin SDK를 사용해 **custom token 생성**
4. 프론트엔드에서 **Firebase Auth로 로그인**

<br />

## ✅ 사전 준비

- Firebase 프로젝트 설정
- Firebase Admin SDK 백엔드에 설치 및 초기화
- 카카오/네이버 API 연동 로직 구현
- 백엔드에서 Firebase 서비스 계정 키(JSON) 필요

<br />

## ✅ 1단계. 프론트: OAuth 로그인 진행 (카카오/네이버)

```tsx
// ex. Kakao
Kakao.Auth.authorize({
  redirectUri: 'https://yourapp.com/auth/kakao/callback'
});

// ex. Naver (redirect 방식)
window.location.href = NAVER_LOGIN_URL;
```

<br />

## ✅ 2단계. 프론트: code 수신 후 백엔드에 전달

```tsx
axios.post('/api/auth/kakao', { code });
```

<br />

## ✅ 3단계. 백엔드: 토큰 요청 → 사용자 정보 조회

```ts
// 카카오 예시
const accessToken = await getKakaoAccessToken(code);
const userInfo = await getKakaoUserInfo(accessToken);
// userInfo.id 또는 email을 통해 유저 식별
```

<br />

## ✅ 4단계. Firebase Custom Token 생성

```ts
import admin from 'firebase-admin';

admin.initializeApp({
  credential: admin.credential.cert(serviceAccount),
});

const uid = `kakao:${userInfo.id}`; // provider 이름 포함 권장
const customToken = await admin.auth().createCustomToken(uid, {
  provider: 'kakao',
  email: userInfo.email,
});
```

<br />

## ✅ 5단계. 프론트: Firebase로 로그인

```ts
import { signInWithCustomToken } from 'firebase/auth';

const auth = getAuth();
signInWithCustomToken(auth, customToken)
  .then(userCredential => {
    console.log('Firebase 로그인 성공:', userCredential.user);
  });
```

<br />

## ✅ 실무 팁

| 항목 | 설명 |
|------|------|
| UID 구성 | `kakao:12345` 형태처럼 provider명을 prefix로 붙이면 중복 방지 |
| 사용자 정보 추가 | createCustomToken에서 전달하는 커스텀 클레임은 로그인 이후 `getIdTokenResult`로 확인 가능 |
| 새 사용자 관리 | `getUser(uid)`로 firebase user 존재 여부 확인 → 없으면 `createUser()`로 생성 |
| 토큰 보안 | Custom Token은 1회성, 발급 직후 바로 사용해야 함 |

<br />

## ✅ 장점

- 카카오/네이버처럼 **Firebase가 직접 지원하지 않는 로그인 시스템도 활용 가능**
- Firebase의 **Realtime DB, Firestore, Auth, Rule 등 모든 기능과 연동 가능**
- 자체 로그인 시스템을 유지하면서 Firebase 기능 활용 가능

<br />

# 🔗 References
- [Firebase - Authenticate with custom system](https://firebase.google.com/docs/auth/web/custom-auth)
- [카카오 로그인 연동 가이드](https://developers.kakao.com/)
- [네이버 로그인 연동 가이드](https://developers.naver.com/)
