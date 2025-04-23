> 📆 Date: 2025-04-23

# 📌 Focus
- Firebase Authentication

<br />

# 📝 Learnings

## ✅ Firebase Authentication이란?

> Firebase에서 제공하는 인증 시스템. 다양한 로그인 방식을 지원하며, **간단한 설정만으로 안전한 인증 흐름을 구축**할 수 있다.

- 이메일/비밀번호 로그인
- 소셜 로그인 (Google, Facebook 등)
- 전화번호 인증
- 익명 인증
- Custom Token 인증

---

## 🚀 시작하기

### 🔧 1. Firebase 프로젝트 설정
- [Firebase 콘솔](https://console.firebase.google.com/)에서 프로젝트 생성
- 인증 → 로그인 방법 → 사용할 로그인 방식 활성화 (예: 이메일/비밀번호)

### 💻 2. 웹 프로젝트에 Firebase 설치

```bash
npm install firebase
```

### 🔥 3. Firebase 초기화

```ts
// firebase.ts
import { initializeApp } from 'firebase/app';
import { getAuth } from 'firebase/auth';

const firebaseConfig = {
  apiKey: '...',
  authDomain: '...',
  projectId: '...',
  appId: '...',
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```

---

## 👤 기본 로그인 흐름 (이메일/비밀번호)

### ✍️ 회원가입

```ts
import { createUserWithEmailAndPassword } from 'firebase/auth';
import { auth } from './firebase';

createUserWithEmailAndPassword(auth, email, password)
  .then((userCredential) => {
    console.log('회원가입 성공:', userCredential.user);
  })
  .catch((error) => {
    console.error('회원가입 실패:', error);
  });
```

### 🔑 로그인

```ts
import { signInWithEmailAndPassword } from 'firebase/auth';

signInWithEmailAndPassword(auth, email, password)
  .then((userCredential) => {
    console.log('로그인 성공:', userCredential.user);
  })
  .catch((error) => {
    console.error('로그인 실패:', error);
  });
```

### 🔒 로그아웃

```ts
import { signOut } from 'firebase/auth';

signOut(auth);
```

---

## 🪪 로그인 상태 유지 & 사용자 추적

```ts
import { onAuthStateChanged } from 'firebase/auth';

onAuthStateChanged(auth, (user) => {
  if (user) {
    console.log('로그인 상태 유지됨', user);
  } else {
    console.log('로그아웃 상태');
  }
});
```

---

## 📡 사용자 인증 토큰 → 백엔드 연동

```ts
const user = auth.currentUser;
const token = await user?.getIdToken();

axios.get('/api/protected', {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

> 이 방식으로 백엔드에서 Firebase 인증된 유저의 토큰을 검증할 수 있음 (Firebase Admin SDK 필요)

---

## 🎨 소셜 로그인 (Google 예시)

```ts
import { GoogleAuthProvider, signInWithPopup } from 'firebase/auth';

const provider = new GoogleAuthProvider();

signInWithPopup(auth, provider)
  .then((result) => {
    console.log('Google 로그인 성공:', result.user);
  })
  .catch((error) => {
    console.error('로그인 실패:', error);
  });
```

---

## 🏢 실무에서의 적용 전략

### 📁 1. 파일 구조 예시

```
src/
├── auth/
│   ├── firebase.ts              # 초기화 및 인스턴스
│   ├── useAuth.ts               # 사용자 상태 추적 훅
│   └── authProvider.tsx         # 전역 context 관리
```

---

### 🪝 2. 사용자 상태 관리 훅

```ts
// useAuth.ts
import { useEffect, useState } from 'react';
import { onAuthStateChanged } from 'firebase/auth';
import { auth } from './firebase';

export const useAuth = () => {
  const [user, setUser] = useState<null | User>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (firebaseUser) => {
      setUser(firebaseUser);
      setLoading(false);
    });

    return () => unsubscribe();
  }, []);

  return { user, loading };
};
```

---

### 🌍 3. Context로 전역 사용자 정보 제공

```tsx
// authProvider.tsx
import { createContext, useContext } from 'react';
import { useAuth } from './useAuth';

const AuthContext = createContext(null);

export const AuthProvider = ({ children }) => {
  const auth = useAuth();

  return <AuthContext.Provider value={auth}>{children}</AuthContext.Provider>;
};

export const useAuthContext = () => useContext(AuthContext);
```

```tsx
// _app.tsx
<AuthProvider>
  <App />
</AuthProvider>
```

---

### 📌 4. 실무 고려 포인트

| 항목 | 설명 |
|------|------|
| 토큰 갱신 | Firebase는 자동 갱신됨. 필요시 `onIdTokenChanged`로 추적 |
| 토큰 만료 시 재로그인 | 프론트에서 에러 코드 보고 처리 |
| role-based auth | Custom Claims + Firebase Admin SDK 활용 |
| 백엔드 검증 | Firebase Admin SDK로 `verifyIdToken(token)` |
| 상태 동기화 | React Query, Zustand 등과 연계 가능 |

---

## ✅ 결론

- Firebase 인증은 빠르게 구축 가능하면서도 확장성 있음
- 실무에서는 사용자 상태 전역 관리, 백엔드 연동, 토큰 처리 방식 설계가 중요
- Context + custom hook 조합이 매우 유용함

---

# 🔗 References
- [Firebase Auth 공식 문서](https://firebase.google.com/docs/auth)
- [Google 로그인 가이드](https://firebase.google.com/docs/auth/web/google-signin)
- [Admin SDK for Token Verification](https://firebase.google.com/docs/auth/admin/verify-id-tokens)

---

파일명으로는 `firebase-auth-guide.md`, 폴더는 `frontend/auth/`를 추천드립니다.  
이 내용은 한 번 잘 정리해두면 팀 onboarding 자료로도 쓸 수 있어요! 😊
