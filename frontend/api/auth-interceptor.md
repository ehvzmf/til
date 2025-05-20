> 📅 Date: 2025-05-20

# 📌 Focus

**Axios Instance + Request Interceptor를 이용한 자동 인증 헤더 처리**

<br />

# 📝 Learnings

## ✅ Code

```ts
import axios from 'axios';

export const axiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
});

export const authAxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  withCredentials: true,
});

authAxiosInstance.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

<br />

## ✅ Request Interceptor

> Axios의 `request interceptor`는 **요청이 서버로 전송되기 직전에 실행되는 중간 처리기**
> 여기서 요청 config를 수정하거나 헤더를 추가할 수 있다.

<br />

## ✅ Flow

| 단계  | 설명                                 |
| --- | ---------------------------------- |
| 1️⃣ | `authAxiosInstance`로 요청 발생         |
| 2️⃣ | 요청 전 `interceptors.request.use` 실행 |
| 3️⃣ | localStorage에서 토큰을 꺼냄              |
| 4️⃣ | `Authorization` 헤더에 토큰을 자동 추가      |
| 5️⃣ | 최종적으로 서버에 토큰 포함된 요청 전송             |

<br />

## ✅ Advantages

| 장점          | 설명                                             |
| ----------- | ---------------------------------------------- |
| **자동화**     | 모든 요청마다 `Authorization`을 수동으로 설정할 필요 없음        |
| **일관성 유지**  | 인증 누락 없이 모든 API 요청에 토큰 자동 포함                   |
| **중앙화된 관리** | 향후 `refresh token` 로직, `401 처리`, 로깅 등 추가 구현 용이 |

```ts
await authAxiosInstance.post('/api/vote', { politician_id: '1234' });
// 내부적으로는 자동으로 Authorization 헤더가 포함됨
```

<br />

## ✅ 기본 구조 복습

```ts
axios.interceptors.request.use(
  (config) => {
    // 요청 보내기 전 config 수정
    return config;
  },
  (error) => {
    // 요청 자체 실패 처리
    return Promise.reject(error);
  }
);
```

→ 여기서 `config.headers.Authorization = 'Bearer ...'` 형식으로 헤더 삽입 가능

<br />

## ✅ Response Interceptor example

```ts
authAxiosInstance.interceptors.response.use(
  (res) => res,
  (err) => {
    if (err.response?.status === 401) {
      // 예: 토큰 만료 → 자동 로그아웃 처리
    }
    return Promise.reject(err);
  }
);
```

→ 토큰 만료, 권한 없음 등의 응답 상황에도 일관된 처리 가능

<br />

## ✅ field

| 팁                       | 설명                             |
| ----------------------- | ------------------------------ |
| `withCredentials: true` | 쿠키 기반 인증 시 필수 옵션 (CORS 허용과 함께) |
| `baseURL`               | 환경변수로 설정 → 개발/운영 환경 분리         |
| `timeout`               | 요청 지연 방지용 타임아웃 설정도 유용          |
