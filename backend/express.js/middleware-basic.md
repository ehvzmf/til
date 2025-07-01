> 📅 Date: 2025-07-01

# 📌 Focus
미들웨어 개념과 동작 원리
  
<br />

# 📝 Learnings

## 📚 미들웨어란?

Express.js에서 요청(request)과 응답(response) 사이에서 실행되는 함수 <br />
클라이언트의 요청이 서버에 도달했을 때, 최종 응답을 보내기 전까지 중간에 거쳐가는 처리 단계

<br /> 

## 🔄 미들웨어 동작 원리
### 기본 구조

```
function middleware(req, res, next) {
    // 미들웨어 로직
    next(); // 다음 미들웨어로 제어권 전달
}
```

### 실행 순서

1. 요청 접수: 클라이언트가 서버에 요청
2. 미들웨어 체인: 등록된 순서대로 미들웨어 실행
3. next() 호출: 다음 미들웨어로 제어권 전달
4. 최종 응답: 라우트 핸들러에서 응답 전송

<br />  

## 🏗️ 미들웨어 유형
### 1. 애플리케이션 레벨 미들웨어
```
const express = require('express');
const app = express();

// 모든 요청에 대해 실행
app.use((req, res, next) => {
    console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
    next();
});

// 특정 경로에만 적용
app.use('/api', (req, res, next) => {
    console.log('API 요청 감지');
    next();
});
```

### 2. 라우터 레벨 미들웨어

```
const router = express.Router();

router.use((req, res, next) => {
    console.log('라우터 미들웨어 실행');
    next();
});

router.get('/users', (req, res) => {
    res.json({ users: [] });
});
```

### 3. 에러 처리 미들웨어
```
// 에러 처리 미들웨어는 4개의 매개변수를 갖는다. 
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ error: '서버 에러 발생' });
});
```

### 4. 내장 미들웨어
```
// JSON 파싱
app.use(express.json());

// URL 인코딩된 데이터 파싱
app.use(express.urlencoded({ extended: true }));

// 정적 파일 서빙
app.use(express.static('public'));
```

### 5. 서드파티 미들웨어
```
const cors = require('cors');
const morgan = require('morgan');

app.use(cors()); // CORS 처리
app.use(morgan('combined')); // 로깅
```

## ⚡ 중요한 개념들
### next() 함수의 역할

- next(): 다음 미들웨어로 제어권 전달
- next(error): 에러 처리 미들웨어로 제어권 전달
- next('route'): 현재 라우터의 나머지 미들웨어 건너뛰기

```
app.get('/users/:id', 
    (req, res, next) => {
        if (req.params.id === '0') {
            next('route'); // 다음 라우트로 건너뜀
        } else {
            next(); // 다음 미들웨어로
        }
    },
    (req, res, next) => {
        res.json({ id: req.params.id });
    }
);

// next('route')로 인해 여기로 이동
app.get('/users/:id', (req, res) => {
    res.json({ error: 'Invalid ID' });
});
```

### 미들웨어 실행 순서
```
app.use((req, res, next) => {
    console.log('1번 미들웨어');
    next();
});

app.use('/api', (req, res, next) => {
    console.log('2번 미들웨어 (API 경로만)');
    next();
});

app.get('/api/users', (req, res) => {
    console.log('3번 라우트 핸들러');
    res.json({ users: [] });
});

// /api/users 요청 시 실행 순서: 1번 → 2번 → 3번
```

## 🛠️ 실용적인 미들웨어 예제
### 1. 인증 미들웨어
```
function authenticateToken(req, res, next) {
    const token = req.headers['authorization'];
    
    if (!token) {
        return res.status(401).json({ error: '토큰이 필요합니다' });
    }
    
    // 토큰 검증 로직
    if (isValidToken(token)) {
        req.user = getUserFromToken(token);
        next();
    } else {
        res.status(403).json({ error: '유효하지 않은 토큰' });
    }
}

// 사용법
app.get('/protected', authenticateToken, (req, res) => {
    res.json({ message: '인증된 사용자만 접근 가능', user: req.user });
});
```

### 2. 요청 시간 측정 미들웨어
```
function requestTimer(req, res, next) {
    req.startTime = Date.now();
    
    res.on('finish', () => {
        const duration = Date.now() - req.startTime;
        console.log(`${req.method} ${req.url} - ${duration}ms`);
    });
    
    next();
}

app.use(requestTimer);
```

### 3. 요청 검증 미들웨어
```
function validateUser(req, res, next) {
    const { name, email } = req.body;
    
    if (!name || !email) {
        return res.status(400).json({ 
            error: '이름과 이메일은 필수입니다' 
        });
    }
    
    if (!email.includes('@')) {
        return res.status(400).json({ 
            error: '유효한 이메일 형식이 아닙니다' 
        });
    }
    
    next();
}

app.post('/users', validateUser, (req, res) => {
    // 검증된 데이터로 사용자 생성
    res.json({ message: '사용자 생성 완료' });
});
```

### 🔍 Summary

* 미들웨어는 요청-응답 사이클의 중간 처리기
* 순차적 실행: 등록 순서대로 실행됨
* next() 필수: 다음 미들웨어로 제어권을 넘겨야 함
* 에러 처리: next(error)로 에러 미들웨어로 전달
* 재사용성: 공통 로직을 미들웨어로 분리하여 재사용

### 💡 Tips
* 미들웨어 순서가 매우 중요함 (인증 → 검증 → 비즈니스 로직)
* 에러 처리 미들웨어는 항상 마지막에 등록
* 성능을 위해 필요한 경로에만 미들웨어 적용
* next() 호출을 잊지 말 것 (무한 대기 방지)
