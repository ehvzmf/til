> 📅 Date: 2025-06-12

# 📌 Focus

**Spring Tool Suite (STS)** – 백엔드(Spring) 개발을 위한 전용 IDE

<br />

# 📝 Learnings

## ✅ STS

\*\*Spring Tool Suite (STS)\*\*는 Java 백엔드 프레임워크인 **Spring**을 쉽게 개발할 수 있도록 만든 \*\*전용 개발 도구(IDE)\*\*입니다.

* **Eclipse 기반**으로 만들어졌음
* Spring Boot, Spring MVC 같은 백엔드 기술을 **자동으로 지원**
* Java 백엔드에서 가장 많이 사용되는 도구 중 하나

<br />

## ✅ STS가 필요한 이유

| 일반 IDE(Eclipse 등) | STS                               |
| ----------------- | --------------------------------- |
| 기본 Java 개발 도구     | Spring에 최적화된 기능 탑재                |
| 수동 설정 많음          | Spring Boot 초기화, 빌드, 서버 실행 등을 자동화 |
| 플러그인 필요함          | Spring, Maven, Gradle 등이 내장되어 있음  |

> 즉, STS는 Spring 백엔드를 쉽게 시작하고 테스트할 수 있도록 만든 **전용 통합 도구**입니다.

<br />

## ✅ 프론트엔드 개발자 입장에서 왜 알아야 하나?

* 실제 서비스는 대부분 **백엔드 API와 통신**해야 함
* 백엔드 팀이 Spring + Java를 쓸 경우 **STS로 개발 중일 가능성 높음**
* 👉 이해가 되면:

  * API가 어떻게 만들어지는지
  * 테스트용 로컬 서버를 어떻게 켜는지
  * CORS 에러가 왜 나는지 등도 감이 잡힘

<br />

## ✅ STS로 할 수 있는 일 (기본 흐름)

1. **Spring Boot 프로젝트 생성**

   * STS에서는 마법사(마우스로 클릭 클릭)로 간단히 생성 가능
   * 필요한 의존성(JPA, Web 등)을 체크로 추가

2. **서버 실행**

   * 한 번 클릭으로 Spring 서버 실행 (`localhost:8080` 등)

3. **REST API 작성**

   * Java 코드로 API를 만듦
     (예: `/api/posts`, `/api/login` 등)

4. **데이터베이스 연동**

   * MySQL, PostgreSQL 등 DB 연결 및 쿼리 작성

5. **빌드 및 패키징**

   * 실제 서비스용 jar 파일로 패키징 가능

> 프론트 기준: 백엔드가 로컬에서 API 서버를 띄우면,
> `http://localhost:8080/api/posts` 같은 주소로 테스트 가능

<br />

## ✅ 프론트 개발자 입장에서 같이 볼만한 기능

| 기능         | 설명                                                              |
| ---------- | --------------------------------------------------------------- |
| API URL 확인 | `@RestController`, `@GetMapping("/api/~~")` 같은 코드로 API 경로 확인 가능 |
| CORS 설정    | `@CrossOrigin("*")` 또는 Java Config 클래스에서 설정함                    |
| Swagger 연동 | 백엔드에서 API 문서를 자동으로 보여주는 기능                                      |
| Postman 대신 | 직접 STS에서 API 호출 테스트 가능 (단, UI는 없음)                              |

<br />

## ✅ STS 설치 경로

* 공식 사이트: [https://spring.io/tools](https://spring.io/tools)
* macOS, Windows 모두 지원
* 설치하면 Eclipse 비슷한 UI가 뜸 (거의 동일)

<br />

## ✅ STS를 쓰는 대표적인 이유 요약

| 이유               | 설명                                          |
| ---------------- | ------------------------------------------- |
| Spring을 위한 전용 도구 | 복잡한 설정 없이 바로 시작 가능                          |
| 서버 실행이 쉬움        | 버튼 클릭으로 서버 구동 가능                            |
| 프로젝트 구조가 명확      | Controller, Service, Repository 분리 구조 기본 제공 |
| API 테스트도 가능      | 로컬 환경에서 백엔드 자체 실행 가능                        |

<br />

# 🔗 References

* [공식 STS 페이지](https://spring.io/tools)
* [Spring Boot 스타터](https://start.spring.io/)
* [Velog – STS 입문기](https://velog.io/tags/STS)

<br />

# 📂 파일 경로 제안

`/backend/tools/sts-intro.md`

---

필요하시면 Spring Boot 기반 백엔드 서버가 실제로 프론트와 어떻게 연결되는지, `@RestController`, `@RequestMapping`, `CORS`, `JSON 응답 구조`까지 이어서 설명드릴 수 있어요!
