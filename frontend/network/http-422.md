> 📅 Date: 2025-04-22

# 📌 Focus
- HTTP 상태 코드: 422 Unprocessable Entity

<br />

# 📝 Learnings

### ✅ 422 Unprocessable Entity란?

> 서버가 **요청 자체는 이해했지만**, **비즈니스 로직 또는 유효성 검사 상 처리할 수 없을 때** 반환하는 상태 코드

- HTTP 1.1에서 정의된 상태 코드
- **요청 형식은 맞지만**, 서버가 기대한 값과 달라 처리가 불가능할 때 사용됨

---

### 🧪 오늘 경험한 사례

```http
GET /api/report?range=week
```

→ 실제로 서버가 기대한 파라미터는 `range=weekly`였음  
→ 프론트엔드에서는 `week`으로 잘못 전송

📍 결과:
- 서버는 요청 자체는 문제없다고 판단 (`GET`, 파라미터 포함, 문법적 오류 없음)
- 하지만 **`week`는 유효한 값이 아님** → `422 Unprocessable Entity` 응답 반환됨

---

### ⚠️ 400 vs 422 차이점

| 상태 코드 | 사용 상황 |
|-----------|------------|
| 400 Bad Request | 요청 자체가 잘못되었을 때 (문법 오류, 파라미터 누락 등) |
| 422 Unprocessable Entity | 문법은 맞지만, **서버의 유효성 로직에 맞지 않을 때** |

---

### ✅ 프론트엔드 처리 팁

- `axios`, `fetch` 등에서 `422` 응답을 잡아 사용자에게 적절한 메시지 표시
- 예: `"유효하지 않은 파라미터입니다. 다시 시도해주세요."`

```ts
try {
  await axios.get('/api/report?range=week');
} catch (err) {
  if (err.response?.status === 422) {
    alert('유효하지 않은 요청 값입니다.');
  }
}
```

---

# 🔗 References
- [MDN - 422](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/422)
- [RFC 4918 - HTTP Extensions for Web Distributed Authoring and Versioning (WebDAV)](https://datatracker.ietf.org/doc/html/rfc4918)

---

파일명 추천: `http-status-422.md`  
기록용으로 적절할 것 같습니다! 😊
