> 📆 Date: 2025-04-24

# 📌 Focus
Firebase Authentication 프로젝트 도입하기

<br />

# 📝 Learnings

## 1️⃣ Create Firebase Project
1. [firebase console](https://console.firebase.google.com/)에서 프로젝트 생성
2. Authentication 선택
3. 로그인 제공업체 > Google 선택
4. 프로젝트 공개용 이름, 지원 이메일 (일단 내꺼) 설정

<br />

## 2️⃣ Install Firebase in Web project
```bash
pnpm add firebase
```

<br />

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

<br />

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

### header에서 로그인 버튼 분기 처리
```tsx
  const token = localStorage.getItem('token');
  const isLoggedIn = !!token;
```

### axiosInstance에서 요청 시 토큰 자동 포함
```ts
axiosInstance.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);
```

### 로그인/회원가입 리다이렉션
소셜 로그인 화면을 팝업 또는 리다이렉션 화면으로 구현 후, 적절히 (홈 화면 등) 이동


<br />
<br />

## 🔥 추가 정보 가져오는 법 (구글 로그인)
> 사용자 동의를 받아 민감한 정보를 추가로 받아오기
> people api 사용
> 여기서는 생일, 성별 필요

### Google Cloud Console 설정
- 내 프로젝트 > API 및 서비스 > OAuth 동의 화면
  - 앱 유형: 외부
  - 앱 이름, 지원 이메일 등 기본 정보 작성 
- 데이터 액세스 > 범위 추가 또는 삭제
  - `user.birthday.read`, `user.gender.read`, `profile.agerange.read` 등 추가
  - 검색해도 없으면 수동 입력
    `https://www.googleapis.com/auth/user.birthday.read`
    `https://www.googleapis.com/auth/user.gender.read`
- 게시 상태가 프로덕션일 경우, 구글에 앱 검토 요청 필수
- 게시 상태를 테스트로 전환 후 테스트 사용자 추가
- People api 사용 설정 [enable](https://console.developers.google.com/apis/api/people.googleapis.com/overview?project=379942709907)

<br />

### 코드 추가 
firebase.ts에 scope 추가
```ts
googleProvider.addScope('https://www.googleapis.com/auth/user.birthday.read');
googleProvider.addScope('https://www.googleapis.com/auth/user.gender.read');
googleProvider.addScope('https://www.googleapis.com/auth/profile.agerange.read');
```

발급받은 access token으로 people api에 정보 요청
```ts
const accessToken = GoogleAuthProvider.credentialFromResult(result)?.accessToken;

const scopeRes = await axios.get('https://people.googleapis.com/v1/people/me?personFields=birthdays,genders',
  {
    headers: {
      Authorization: `Bearer ${accessToken}`,
    },
  }
);
console.log(scopeRes);
```

<br />

### Response
```
{
  "resourceName": "people/...",
  "etag": "...",
  "genders": [
    {
      "metadata": {},
      "value": "female | male",
      "formattedValue": "female | male",
    },
  ],
  "birthdays": [
    {
      "metadata": {},
      "date": {
        "year": 2000,
        "month": 7,
        "day": 8,
    },
  ]
```
- ⚠️ 기록되지 않은 필드는 아예 생략된 채 온다.
  - resourceName, etag만 전송될 수 있다.
  - people api는 잘 사용하고 있다는 의미
- 응답이 2개씩 오는 이유는 여러 도메인에서 중복 저장하기 때문이라고 한다.
  - `primary === true`인 항목 사용: 가장 대표적인 정보
  - `source.type === 'ACCOUNT'`인 항목 사용: 사용자가 직접 설정한 정보
  - `birthdays[0]`만 사용: 간단한 처리, 위험 요소 존재
  ```
  const birthday = birthdays.find(
    (b) => b.metadata?.primary || b.metadata?.source?.type === 'ACCOUNT'
  )?.date;
  
  if (birthday) {
    const { year, month, day } = birthday;
    console.log(`사용자 생일: ${year}-${month}-${day}`);
  }
  ```
<br />

# 🔗 References
- [People API 문서](https://developers.google.com/people)
- [Firebase Authentication Docs](https://firebase.google.com/docs/auth/web/start)
- [OAuth 민감 범위 및 검토 정책](https://support.google.com/cloud/answer/9110914)
