> 📅 Date: 2025-04-16

# 📌 Focus
- CORS error가 나는 상황.
- 백이 해결해주길 때까지 손가락만 빨며 기다리지 말고 알아서 무시해서 쓰자. 
<br />

# 📝 Learnings
### Vite(or Webpack) Proxy
프론트 서버가 중간에 API 요청을 프록시해주는 중계 역할 -> CORS 회피

**적용 예시 (vite.config.ts)**
```
server: {
  proxy: {
    '/api': {
      target: 'http://0.0.0.0:8000', // 백엔드 주소
      changeOrigin: true,
      // rewrite: path => path.replace(/^\/api/, '') // /api/ 부분 대체. 나는 필요 없었음
    },
  },
}
```
사용 시에는 /api/~ 넣으면 request가 localhost:5173/api/~로 날아감
```
axios.get('/api/address');
```
- 보안 정책 위반 없음, 정석적인 개발용 방법
- 프론트 dev server에서만 작동 (운영 배포 환경에서는 의미 없음)
- 늘 설정이 잘 안됐는데 성공적으로 작동! 제일 깔끔하다.

<br />

**브라우저 보안 비활성화 (Headless or not)**
```
# Windows
chrome.exe --disable-web-security --user-data-dir="C:/chrome-temp"

# macOS
open -na "Google Chrome" --args --disable-web-security --user-data-dir="/tmp/chrome-temp"
```
- 어떤 API든 CORS 없이 바로 요청 가능
- 백엔드 수정 없이 바로 테스트 가능
- 보안 완전 무력화, 브라우저를 실제로 쓰면 안됨
- 일부 쿠키/로그인 기능은 비정상 작동

<br />

**중간 프록시 서버 사용**
`npx cors-anywhere` | `http-proxy-middleware`
`axios.get('http://localhost:8080/http://0.0.0.0:8000/api/address');`
- 프론트에서 주소만 바꾸면 됨, 간단
- 팀 통합 테스트용으로 빠르게 사용 가능
- 주소 복잡, 유지보수 불편, 관리 대상이 늘어남

<br />

### 백엔드에서 CORS 헤더 직접 허용
```
// FastAPI 예시
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
  CORSMiddleware,
  allow_origins=["http://localhost:5173"],  # 또는 ["*"]
  allow_credentials=True,
  allow_methods=["*"],
  allow_headers=["*"],
)
```
```
// WebMvcConfigurer 사용
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**") // 모든 경로 허용
                .allowedOrigins("http://localhost:5173") // 프론트 주소
                .allowedMethods("*") // GET, POST, PUT 등
                .allowedHeaders("*")
                .allowCredentials(true); // 필요 시 true
    }
}
```
```
// @CrossOrigin Annotation
@CrossOrigin(origins = "http://localhost:5173")
@RestController
@RequestMapping("/api/v1")
public class PoliticianController {

    @GetMapping("/politicians/live")
    public List<Politician> getLiveData() {
        // ...
    }
}
```
```
// Spring Security 켜져 있으면 SecurityFilterChain에서 CORS 설정 포함 
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .cors(withDefaults()) // CORS 사용
        .csrf().disable() // 개발 중이면 비활성화
        .authorizeHttpRequests()
        .anyRequest().permitAll();

    return http.build();
}

@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(List.of("http://localhost:5173"));
    configuration.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    configuration.setAllowedHeaders(List.of("*"));
    configuration.setAllowCredentials(true);

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);
    return source;
}

```
- 정석, 가장 확실하고 안전함
- 운영 환경에서도 사용 가능
- 잘못 설정하면 보안 구멍

<br />

### Nginx 리버스 프록시로 우회 (운영배포용)
```
location /api/ {
  proxy_pass http://localhost:8000/;
  proxy_set_header Host $host;
}
```
- 운영 환경에서 CORS 에러 없이 API 통합 가능
- 보안 및 도메인 정책 같이 설정할 수 있음
- Nginx 설정을 따로 해야 하는 번거로움
- 로컬 개발 환경에선 적용하기 힘든 편

<br />

### Finally
백엔드 CORS 설정 수정 완료, 혹시 몰라서 Vite.config.ts에서 proxy 설정
