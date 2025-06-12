> 📅 Date: 2025-06-12

# 📌 Focus

**Swagger + Spring Boot로 API 문서 자동화하기**

<br />

# 📝 Learnings

## ✅ Swagger

> REST API를 시각적으로 문서화하고 테스트까지 가능한 도구

* 개발자가 만든 API를 자동으로 수집해서 브라우저에서 확인 가능한 문서 형태로 제공한다.
* 각 API에 대해:

  * URL
  * HTTP 메서드(GET/POST 등)
  * 요청 파라미터
  * 응답 구조
  * 설명 문구
    까지 자동 정리된다. 
* 직접 요청도 해볼 수 있다. (프론트가 없어도) 

<br />

## ✅ Spring Boot + Swagger 자동 문서 흐름

Spring Boot > `springdoc-openapi` 라이브러리를 사용해
작성된 API 코드를 기반으로 Swagger UI를 자동 생성할 수 있다.

### 흐름 요약:

```plaintext
Java 코드 작성 → @RestController + @GetMapping 등 → Swagger UI에서 자동 반영
```

<br />

## ✅ 설치 및 설정

### 1. Gradle/Maven 의존성 추가

#### Gradle

```groovy
dependencies {
  implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.0.4'
}
```

#### Maven

```xml
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.0.4</version>
</dependency>
```

버전은 [공식 문서](https://springdoc.org/)에서 확인 가능

---

### 2. 서버 실행 후 접속 경로

```plaintext
http://localhost:8080/swagger-ui/index.html
```

* 로컬에서 Spring 서버 실행 후 브라우저에서 접속하면 자동 생성된 Swagger 문서 확인 가능

<br />

---

<br />

## ✅ 자동 문서 구조 예시

컨트롤러에 아래와 같은 코드가 있다면:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

  @GetMapping
  public List<User> getUsers() {
    return userService.findAll();
  }

  @PostMapping
  public User createUser(@RequestBody User user) {
    return userService.save(user);
  }
}
```

Swagger UI에서는 자동으로 아래와 같은 문서를 생성:

* `/api/users [GET]`: 사용자 목록 조회
* `/api/users [POST]`: 사용자 생성 (요청 body에 name, email 포함)
* 각 요청에 대해 try it out 버튼으로 직접 API 테스트 가능

<br />

## ✅ Swagger UI에서 확인 가능한 정보

| 항목          | 설명                           |
| ----------- | ---------------------------- |
| API URL     | `/api/users` 등               |
| HTTP Method | GET / POST / PUT / DELETE    |
| 요청 타입       | Query, Path, Body 등 입력 방식 구분 |
| 요청 예시       | 샘플 JSON 입력 폼                 |
| 응답 예시       | HTTP 200 응답 형식 및 필드 구조 미리보기  |
| Description | 메서드 설명                       |

<br />

## ✅ 문서 내용에 설명 추가하기

```java
@Operation(summary = "모든 사용자 조회", description = "DB에 저장된 사용자 목록을 반환합니다.")
@GetMapping
public List<User> getUsers() {
  return userService.findAll();
}
```

→ Swagger UI에서 API 설명 문구가 표시됨

<br />

## ✅ 응답 객체에 스키마 정의하기

```java
@Schema(description = "사용자 모델")
public class User {

  @Schema(description = "사용자 고유 ID")
  private Long id;

  @Schema(description = "사용자 이름", example = "홍길동")
  private String name;

  ...
}
```

→ Swagger에서 자동으로 필드 구조 및 예시 출력됨

<br />

## ✅ TIPS

* 백엔드에서 API 만들고 문서 따로 작성할 필요 없음
* 프론트는 Swagger 보면서 직접 요청 구조 확인 가능
* JSON 형식, 응답 구조, 필드 예시를 실시간으로 확인
* 로컬 환경에서 API 테스트 가능 → Postman 없이 가능
* 테스트용 토큰, 헤더도 Swagger에서 입력 가능

<br />

# 🔗 References

* [Springdoc 공식 문서](https://springdoc.org/)
* [Swagger 공식](https://swagger.io/tools/swagger-ui/)
* [Baeldung - Spring Boot + OpenAPI 3](https://www.baeldung.com/spring-rest-openapi-documentation)
