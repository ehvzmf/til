> 📆 Date: 2025-04-23

# 📌 Focus
Firebase Authentication 프로젝트 도입하기

<br />

# 📝 Learnings

## 1️⃣ Create Firebase Project
1. [firebase console](https://console.firebase.google.com/)에서 프로젝트 생성
2. Authentication 선택
3. 로그인 제공업체 > Google 선택
4. 프로젝트 공개용 이름, 지원 이메일 (일단 내꺼) 설정

## 2️⃣ Install Firebase in Web project
```bash
pnpm add firebase
```

## 3️⃣ Initialize Firebase
src/shared/firebase.ts
```ts
import { initializeApp } from 'firebase/app';
import { getAuth } from 'firebase/auth';

// .env에서 환경변수 불러오기
const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```
- 변수는 `.env`에서 관리
- 내 앱 추가 > 그대로 복붙하면 됨

## 4️⃣ Google 로그인 연결
### GoogleAuthProvider 추가
```ts
// firebase.ts에 추가
import { getAuth, GoogleAuthProvider } from 'firebase/auth';
export const googleProvider = new GoogleAuthProvider();
```

### button handler 생성
```tsx
import { auth, googleProvider } from '@/shared/api/firebase';
import { signInWithPopup } from 'firebase/auth';

const handleGoogleLogin = async () => {
  try {
    const result = await signInWithPopup(auth, googleProvider);
    const user = result.user;
    const token = await user.getIdToken();

    localStorage.setItem('token', token);
    console.log('로그인 성공:', user);
    
    localStorage.setItem('user', JSON.stringify({
      uid: user.uid,
      email: user.email,
      displayName: user.displayName,
      photoURL: user.photoURL,
    }));

    window.location.href = '/';
  } catch (error) {
    console.error('로그인 실패:', error);
    alert('로그인 실패');
  }
};
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
