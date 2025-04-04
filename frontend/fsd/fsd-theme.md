> 📅 Date: 2025-04-01

# 📌 Focus
- FSD (Feature-Sliced Design)
<br />

# 📝 Learnings
### FSD에서 `styles` 위치
```
src/
├── app/
│   ├── config/         # 앱 초기화용 설정
│   ├── providers/      # 전역 프로바이더
│   └── routes/         # 라우팅 정의
├── shared/
│   ├── styles/
│   │   └── theme.tsx   # 전역 테마 정의
│   ├── ui/             # 공통 UI 컴포넌트
│   └── lib/            # 유틸리티 함수
├── features/
├── entities/
└── pages/
```
- **`shared/` 레이어**에 위치
- `shared/` 레이어의 역할은 **공통성**, **재사용성** -> 애플리케이션 전반에서 사용되는 공통적인 코드
- `utils`, 공통 UI 컴포넌트, 전역 설정
- **전역 테마**는 특정 기능/도메인에 종속되지 않고 모든 레이어에서 재사용되기 때문에 공통되는 리소스를 관리하는 `shared/`에 위치하는 게 적절하다.
- 확장성과 유지보수성 고려
<br />

### app 레이어의 역할
- 애플리케이션 초기화 및 설정
- routing, global provider, analytics 같은 초기화 및 환경 설정 코드
- `app/`에 위치하지 않는 이유: 관심사의 혼동 방지
<br />

=> **전역 설정**(환경 변수, 앱 초기화에 필요한 설정)과 **전역 리소스**(특정 초기화 과정 없이 어디서나 재사용 가능) 구분!

<br />

### 그러나 아직 헷갈리는 이유
https://github.com/feature-sliced/examples/tree/master/todo-app/src/app/styles
공식에서 제공한 예시는 또 다르다...
