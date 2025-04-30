> 📅 Date: 2025-04-30

# 📌 Focus  
- Firebase Admin SDK: 사용자 생성 및 토큰 검증

<br />

# 📝 Learnings

## ✅ Firebase Admin SDK 초기화

```ts
import admin from 'firebase-admin';
import serviceAccount from './firebase-service-account.json'; // Firebase 서비스 계정 JSON

admin.initializeApp({
  credential: admin.credential.cert(serviceAccount),
});
```

<br />

## ✅ 사용자 조회 / 생성

### 사용자 존재 여부 확인

```ts
const uid = 'kakao:123456'; // 고유 식별자 (ex. kakao, naver 등 prefix 사용 권장)

try {
  const user = await admin.auth().getUser(uid);
  console.log('기존 사용자', user.uid);
} catch (error) {
  if (error.code === 'auth/user-not-found') {
    console.log('신규 사용자, 생성 필요');
  } else {
    throw error;
  }
}
```

<br />

### 사용자 생성

```ts
await admin.auth().createUser({
  uid: uid,
  email: 'example@kakao.com',
  displayName: '홍길동',
  photoURL: 'https://k.kakaocdn.net/...'
});
```

- `uid`는 반드시 고유해야 함
- 필요한 정보는 카카오/네이버 사용자 정보에서 추출

<br />

## ✅ Custom Token 발급

```ts
const customToken = await admin.auth().createCustomToken(uid, {
  provider: 'kakao',
});
```

- `provider`는 클레임(custom claims)으로 추가 가능
- 클라이언트는 이 토큰으로 Firebase에 로그인

<br />

## ✅ 클라이언트에서 로그인

```ts
import { signInWithCustomToken } from 'firebase/auth';

const userCredential = await signInWithCustomToken(auth, customToken);
console.log(userCredential.user);
```

<br />

## ✅ Firebase ID Token 검증

- 사용자가 로그인한 후 API 요청 시, ID 토큰을 헤더에 담아 전달해야 함

```ts
// 프론트 → 백엔드 요청
Authorization: Bearer <id_token>
```

- 백엔드에서 토큰 검증:

```ts
const decodedToken = await admin.auth().verifyIdToken(idToken);
console.log('인증된 사용자 UID:', decodedToken.uid);
```

- 검증 실패 시 401 Unauthorized 응답 처리 필요

<br />

## ✅ 요약

| 기능 | 코드 |
|------|------|
| 사용자 조회 | `admin.auth().getUser(uid)` |
| 사용자 생성 | `admin.auth().createUser({...})` |
| 커스텀 토큰 생성 | `admin.auth().createCustomToken(uid)` |
| ID 토큰 검증 | `admin.auth().verifyIdToken(token)` |

<br />

# 🔗 References
- [Firebase Admin SDK (Node.js)](https://firebase.google.com/docs/admin/setup)
- [Custom Token 생성 가이드](https://firebase.google.com/docs/auth/admin/create-custom-tokens)
- [ID Token 검증 가이드](https://firebase.google.com/docs/auth/admin/verify-id-tokens)
