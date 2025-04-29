> 📆 2025-04-29

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
