> 📅 Date: 2025-06-12

# 📌 Focus

Spring Boot 백엔드 서버와 프론트엔드 연결 구조
(REST API 기반, JSON 통신, 포트 구조, CORS...)

<br />

# 📝 Learnings

## ✅ Basic

Spring Boot: Java 백엔드 웹 프레임워크. REST API 서버를 쉽게 구현할 수 있는 도구

프론트엔드와 HTTP 요청/응답으로 연결된다. (REST API)

> ✔️ 프론트는 React, Vue, 등 HTML + JS로 UI를 보여주고
> ✔️ 백엔드는 Spring Boot가 API를 만들어서 데이터를 제공합니다.

<br />

## ✅ 대략적인 흐름

```plaintext
사용자 → 브라우저 → React 앱 → fetch('/api/users')
                              ↓
                        Spring Boot 서버 (/api/users)
                              ↓
                        DB 조회 → JSON 응답
```

`/api/users`로 요청하면

1. React에서 `fetch('/api/users')`로 요청
2. Spring Boot가 컨트롤러에서 이 URL을 처리
3. DB나 내부 로직 처리 후 JSON으로 응답
4. 프론트는 응답 데이터를 화면에 렌더링

<br />

## ✅ 실제 코드 흐름

### 🔸 1. 프론트: API 요청

```tsx
// React
useEffect(() => {
  fetch('http://localhost:8080/api/users')
    .then(res => res.json())
    .then(data => setUsers(data));
}, []);
```

### 🔸 2. 백엔드: Spring Boot Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

  @GetMapping
  public List<User> getAllUsers() {
    return userService.getUsers(); // DB에서 가져옴
  }
}
```

→ 이때 `User` 객체는 자동으로 **JSON**으로 직렬화

```json
[
  { "id": 1, "name": "Kim" },
  { "id": 2, "name": "Lee" }
]
```
<br />
---
<br />
## ✅ 포트 연결 구조

| 구성          | 기본 포트  |
| ----------- | ------ |
| React 앱     | `3000` |
| Spring Boot | `8080` |

⇒ 서로 다른 포트이기 때문에 **CORS 문제**가 발생

<br />
---
<br />

## ✅ CORS 설정 (Cross-Origin Resource Sharing)

> 브라우저는 서로 다른 포트/도메인의 요청을 보안 상 차단
> → 그래서 백엔드가 프론트의 요청을 허용해주도록 CORS 설정을 필요

### 방법 1: 컨트롤러 레벨에서 허용

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
@RequestMapping("/api/users")
public class UserController {
  ...
}
```

<br />

### 방법 2: 전체 CORS 설정

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/**")
      .allowedOrigins("http://localhost:3000")
      .allowedMethods("*");
  }
}
```

<br />

---

<br />

## ✅ 데이터 형식

프론트와 백엔드는 **JSON**으로 통신

FE:

```json
{ "name": "Kim", "age": 28 }
```

BE: 

```java
@PostMapping
public void createUser(@RequestBody User user) {
  System.out.println(user.getName());
}
```

<br />

---

<br />

## ✅ 파일 기반 통신 (업로드/다운로드)

* `@RequestParam MultipartFile file`: 프론트에서 파일 업로드 받을 때
* 파일 다운로드는 응답 타입을 `application/octet-stream` 등으로 설정

<br />

---

<br />

## ✅ 실제 협업 시 구조

```plaintext
/frontend
  - React/Vite 프로젝트
  - axios 또는 fetch로 Spring API 호출

/backend
  - Spring Boot 프로젝트
  - Controller → Service → Repository → DB

🌐 연결: 프론트는 3000번 포트에서 개발 중,
        백엔드는 8080번 포트에서 API 제공
```

<br />

# 🔗 References

* [Spring 공식 문서 - RestController](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-restcontroller)
* [Spring Boot CORS Guide](https://spring.io/guides/gs/rest-service-cors/)
* [MDN - CORS 개념](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
