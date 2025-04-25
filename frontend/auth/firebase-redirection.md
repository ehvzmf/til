> 📆 2025-04-25

# 📌 Focus
~~기존에는 popup으로 구현했던 구글 로그인(firebase 인증)을 리다이렉션으로 구현해보자~~
배포 이후에 구현 가능하다고 한다... 수많은 삽질 끝에 개발 환경에선 실패했다. 

<br />

# 📝 Redirect 
> 소셜 로그인 시 팝업 대신 로그인 페이지로 리디렉션 > 로그인 완료 후 원래 페이지로 돌아오는 방식
- 팝업 차단 이슈 회피 가능
- 모바일 환경에 적합
- 로그인 직후 앱을 재초기화하므로 상태 관리 주의

<br />

# 🎢 How to?

### Google authProvider 설정 및 메서드 선택 
```ts
import { GoogleAuthProvider, signInWithRedirect } from 'firebase/auth';

const handleGoogleSignup = () => {
  signInWithRedirect(auth, googleProvider);
};
```

<br />

### 로그인 콜백
**Client**
AuthCallback 페이지 생성 후 라우팅 추가
```{ path: '/auth/callback', element: <AuthCallback />}```

```tsx
import { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { getRedirectResult, GoogleAuthProvider } from 'firebase/auth';
import { auth } from '@/shared/api/firebase';
import axios from 'axios';

const AuthCallback = () => {
  const navigate = useNavigate();
  useEffect(() => {
    const handleRedirect = async () => {
      try {
        const result = await getRedirectResult(auth);
        if (!result) {
          alert('로그인 정보가 없습니다.');
          navigate('/login');
          return;
        }

        const user = result.user;
        console.log('Redirect result:', result);

        const token = await user.getIdToken();
        localStorage.setItem('token', token);

        navigate('/signup/form', {
          state: {
            user_uid: verifyRes.data.user_uid,
            email: verifyRes.data.email,
            social_provider: verifyRes.data.social_provider,
            picture: verifyRes.data.picture,
          },
        });
      handleRedirect();
  }, [navigate]);

  return <div>로그인 처리 중입니다...</div>;
};

export default AuthCallback;
```

<br />

### Google Cloud Console에서 URI 등록
- Cloud Console > API 및 서비스 > 사용자 인증 정보 
- 클라이언트ID에서 승인된 리디렉션 URI 등록
- ⚠️디폴트로 있던 --firebaseapp.com 삭제

<br />

### 어차피 호스팅해야 쓸 수 있다. 
- `firebase.ts`에서 사용하는 auth_domain도 일치하는지 체크
- 승인된 Javascript 원본도 체크
- 내 도메인에 따라 https://mydomain/ __/auth/handler ~~ 로 연결될 건데 https 연결 때문에 개발 환경에서는 사용할 수 없다.
  - mkcert도 깔아보고 테스트해볼 방법 없나 계속 해봤는데 안됨
  - https 때문에 보안 연결이 안되거나 404 에러가 남.
  - firebase 호스팅 후 firebase.json rewrites까지 추가 설정 필요
- 그냥 팝업하고 배포 후 다시 시도해보기로 했다. 
