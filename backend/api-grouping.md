> 📅 Date: 2025-06-13

# 📌 Focus

- API 그룹 나누기 (API Grouping)
- RESTful API 설계
- 백엔드 아키텍처
- 프론트엔드-백엔드 협업
  
<br />

# 📝 Learnings

**API 그룹 나누기**

백엔드에서 제공하는 API 엔드포인트들을 논리적이고 체계적으로 분류하고 구조화하는 과정. <br />
단순히 URL 경로를 정리하는 것을 넘어서, 전체 시스템의 아키텍처와 개발 생산성에 직접적인 영향을 미치는 중요한 설계 결정이다. <br /> <br />
(FE) 잘 구조화된 API는 더 직관적이고 예측 가능한 데이터 페칭 로직을 작성할 수 있게 해준다. <br />
반대로 무질서한 API 구조는 프론트엔드 코드의 복잡성을 증가시키고 유지보수를 어렵게 만든다.

<br />

## 방법론

### 1. Resource-based Grouping

* 가장 일반적이고 RESTful한 접근 방식
* 데이터 엔티티를 중심으로 API를 그룹화한다.
  

```jsx
// 사용자 관련 API
GET    /api/users           // 사용자 목록 조회
POST   /api/users           // 사용자 생성
GET    /api/users/:id       // 특정 사용자 조회
PUT    /api/users/:id       // 사용자 정보 수정
DELETE /api/users/:id       // 사용자 삭제

// 게시글 관련 API
GET    /api/posts           // 게시글 목록 조회
POST   /api/posts           // 게시글 생성
GET    /api/posts/:id       // 특정 게시글 조회
PUT    /api/posts/:id       // 게시글 수정
DELETE /api/posts/:id       // 게시글 삭제

// 댓글 관련 API (중첩 리소스)
GET    /api/posts/:postId/comments     // 특정 게시글의 댓글 목록
POST   /api/posts/:postId/comments     // 댓글 생성
DELETE /api/comments/:id               // 댓글 삭제

```

**프론트엔드에서의 활용:**

```tsx
// React + TypeScript 예시
interface User {
  id: number;
  name: string;
  email: string;
}

interface Post {
  id: number;
  title: string;
  content: string;
  authorId: number;
}

// API 클라이언트 구조화
class UserAPI {
  static async getUsers(): Promise<User[]> {
    const response = await fetch('/api/users');
    return response.json();
  }

  static async createUser(userData: Omit<User, 'id'>): Promise<User> {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(userData)
    });
    return response.json();
  }
}

class PostAPI {
  static async getPosts(): Promise<Post[]> {
    const response = await fetch('/api/posts');
    return response.json();
  }

  static async getPost(id: number): Promise<Post> {
    const response = await fetch(`/api/posts/${id}`);
    return response.json();
  }
}

```

### 2. Feature-based Grouping

비즈니스 로직이나 사용자 기능을 중심으로 API를 그룹화하는 방식

```jsx
// 인증 관련 API
POST /api/auth/login        // 로그인
POST /api/auth/logout       // 로그아웃
POST /api/auth/refresh      // 토큰 갱신
POST /api/auth/register     // 회원가입

// 검색 관련 API
GET  /api/search/users      // 사용자 검색
GET  /api/search/posts      // 게시글 검색
GET  /api/search/global     // 통합 검색

// 알림 관련 API
GET  /api/notifications     // 알림 목록
PUT  /api/notifications/:id/read  // 알림 읽음 처리
POST /api/notifications/settings  // 알림 설정

```

**프론트엔드에서의 활용:**

```tsx
// 기능별 커스텀 훅 구조화
const useAuth = () => {
  const [user, setUser] = useState<User | null>(null);

  const login = async (credentials: LoginCredentials) => {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(credentials)
    });
    const userData = await response.json();
    setUser(userData);
  };

  const logout = async () => {
    await fetch('/api/auth/logout', { method: 'POST' });
    setUser(null);
  };

  return { user, login, logout };
};

const useSearch = () => {
  const [results, setResults] = useState([]);

  const searchUsers = async (query: string) => {
    const response = await fetch(`/api/search/users?q=${query}`);
    const data = await response.json();
    setResults(data);
  };

  return { results, searchUsers };
};

```

### 3. Version-based Grouping

API의 하위 호환성을 유지하면서 새로운 기능을 추가할 때 사용하는 방식

```jsx
// v1 API (레거시)
GET /api/v1/users
GET /api/v1/posts

// v2 API (현재 버전)
GET /api/v2/users      // 추가 필드 포함
GET /api/v2/posts      // 개선된 응답 구조

// v3 API (최신 버전)
GET /api/v3/users      // GraphQL 스타일 필드 선택 지원
GET /api/v3/posts      // 실시간 업데이트 지원

```

**프론트엔드에서의 버전 관리:**

```tsx
// API 버전 관리 유틸리티
const API_VERSION = 'v2';

class APIClient {
  private baseURL: string;

  constructor(version: string = API_VERSION) {
    this.baseURL = `/api/${version}`;
  }

  async get<T>(endpoint: string): Promise<T> {
    const response = await fetch(`${this.baseURL}${endpoint}`);
    return response.json();
  }
}

// 사용 예시
const apiV2 = new APIClient('v2');
const apiV3 = new APIClient('v3');

// 점진적 마이그레이션
const users = await apiV2.get<User[]>('/users');  // 안정적인 v2 사용
const posts = await apiV3.get<Post[]>('/posts');  // 새로운 기능은 v3 사용

```

## 고급 그룹핑 전략

### 1. Domain-driven Grouping

대규모 애플리케이션에서 비즈니스 도메인을 기준으로 API를 분리

```jsx
// 사용자 도메인
/api/user-management/users
/api/user-management/profiles
/api/user-management/preferences

// 콘텐츠 도메인
/api/content-management/posts
/api/content-management/comments
/api/content-management/media

// 결제 도메인
/api/payment/orders
/api/payment/transactions
/api/payment/billing

```

### 2. CQRS 패턴 기반 그룹핑

Command와 Query를 분리하여 읽기와 쓰기 작업을 명확히 구분한다.

```jsx
// Command API (쓰기 작업)
POST /api/commands/create-user
POST /api/commands/update-post
POST /api/commands/delete-comment

// Query API (읽기 작업)
GET  /api/queries/users
GET  /api/queries/posts
GET  /api/queries/user-posts/:userId

```

**프론트엔드에서의 CQRS 활용:**

```tsx
// 명령과 조회를 분리한 훅
const useUserCommands = () => {
  const createUser = async (userData: CreateUserRequest) => {
    return await fetch('/api/commands/create-user', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(userData)
    });
  };

  return { createUser };
};

const useUserQueries = () => {
  const [users, setUsers] = useState<User[]>([]);

  const fetchUsers = async () => {
    const response = await fetch('/api/queries/users');
    const data = await response.json();
    setUsers(data);
  };

  return { users, fetchUsers };
};

```

## 프론트엔드 개발자를 위한 고려사항

### 1. 프론트엔드 컴포넌트 구조와의 정렬

API 그룹핑은 프론트엔드의 컴포넌트 구조와 일치하도록 설계하는 것이 좋다.

```tsx
// 컴포넌트 구조
src/
  components/
    user/
      UserProfile.tsx
      UserList.tsx
    post/
      PostDetail.tsx
      PostList.tsx
    comment/
      CommentList.tsx

// 대응하는 API 구조
/api/users/
/api/posts/
/api/comments/

```

### 2. 상태 관리와의 연동

Redux나 Zustand 같은 상태 관리 라이브러리의 구조와 API 그룹핑을 일치시키면 코드 일관성을 높일 수 있다.

```tsx
// Redux slice 구조
interface RootState {
  users: UsersState;
  posts: PostsState;
  comments: CommentsState;
}

// 대응하는 API 서비스
class UserService {
  static baseURL = '/api/users';
  // ...
}

class PostService {
  static baseURL = '/api/posts';
  // ...
}

```

### 3. TypeScript 타입 정의와의 일관성

API 그룹핑과 TypeScript 타입 정의를 일관되게 유지한다.

```tsx
// types/api.ts
namespace API {
  namespace Users {
    interface GetUsersResponse {
      users: User[];
      total: number;
    }

    interface CreateUserRequest {
      name: string;
      email: string;
    }
  }

  namespace Posts {
    interface GetPostsResponse {
      posts: Post[];
      hasMore: boolean;
    }
  }
}

```

## 실전 적용 팁

### 1. API 문서화 도구 활용

Swagger/OpenAPI를 사용하여 그룹화된 API를 문서화하면 프론트엔드 개발자가 더 쉽게 이해하고 사용할 수 있다.

```yaml
# OpenAPI 3.0 예시
paths:
  /api/users:
    get:
      tags:
        - Users
      summary: Get all users
      responses:
        '200':
          description: Successful response

  /api/posts:
    get:
      tags:
        - Posts
      summary: Get all posts
      responses:
        '200':
          description: Successful response

```

### 2. API 클라이언트 코드 생성

OpenAPI 스펙을 기반으로 TypeScript 클라이언트 코드를 자동 생성하여 프론트엔드에서 타입 안전성을 보장할 수 있다.

```bash
# OpenAPI Generator 사용 예시
npx @openapitools/openapi-generator-cli generate \
  -i api-spec.yaml \
  -g typescript-fetch \
  -o src/api/generated

```

### 3. 모니터링 및 메트릭

API 그룹별로 사용량, 응답 시간, 에러율 등을 모니터링하여 개선점을 찾을 수 있다.

```tsx
// API 호출 로깅 예시
const apiCall = async (endpoint: string, options?: RequestInit) => {
  const startTime = Date.now();
  const group = endpoint.split('/')[2]; // /api/users -> users

  try {
    const response = await fetch(`/api${endpoint}`, options);
    const duration = Date.now() - startTime;

    // 메트릭 수집
    analytics.track('api_call', {
      group,
      endpoint,
      duration,
      status: response.status
    });

    return response;
  } catch (error) {
    analytics.track('api_error', {
      group,
      endpoint,
      error: error.message
    });
    throw error;
  }
};

```

<br />

# 🔗 References

- [RESTful API Design Best Practices](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design)
- [OpenAPI Specification](https://swagger.io/specification/)
- [Domain-Driven Design in APIs](https://martinfowler.com/articles/richardsonMaturityModel.html)
- [CQRS Pattern Documentation](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs)
- [API Versioning Strategies](https://www.troyhunt.com/your-api-versioning-is-wrong-which-is/)
