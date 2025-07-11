> 📅 Date: 2025-07-11


# 📌 Focus
- TypeScript
- index.ts 모듈 re-export: 상대경로 vs 절대경로

<br />

# 📝 Learnings
#### 📌 폴더마다 index를 두는 이유
> - 폴더 단위로 public API(외부에 노출할 컴포넌트/함수 등)를 명확하게 관리 가능
> - 상위 폴더나 다른 곳에서 import할 때, 세부 파일 구조를 신경 쓸 필요 없음 <br/>
>   `import { Button } from '@/shared/ui'` < 내부 파일명이 바뀌어도 index만 유지
> - 폴더 내부 구현 세부사항(파일명, 구조) 캡슐화
> - 폴더 내 여러 파일을 한 번에 export할 수 있어 생산성 향상, 관리 용이
> - 리팩토링 시 import 구문 최소 변경

<br />

#### 📌 특히, FSD(Feature-Sliced Design)에서의 이점
- FSD 구조는 features, entities, shared 등 폴더로 역할을 분리
- 각 도메인/슬라이스마다 index.ts 작성 시, 해당 폴더의 "공식 입구" 역할
- 상위/외부에서 import 경로가 단순해짐 → 유지보수성, 가독성 상승
- 컴포넌트, 유틸, 타입 등 폴더별 public API만 노출 가능
- 내부 구현 숨기고, 외부는 index만 바라보면 됨
- 구조 리팩토링(폴더명/위치 변경)에도 import 구문 영향 최소화

<br />

### ❔index.ts에서 여러 컴포넌트를 한 번에 내보낼 때 경로 방식은?
- 상대경로(`./컴포넌트명`) vs 절대경로(`@/폴더/컴포넌트명`)
- 결론: **index.ts 내부에서는 상대경로 사용이 권장됨**

<br />

## 상대경로를 써야 하는 이유
- 번들러가 경로 해석을 단순하게 처리 → 빌드 속도, 번들 크기 최적화
- 폴더 구조 변경 시 내부 참조 유지, 유지보수 용이
- 내부 컴포넌트 간 순환 참조 위험 감소
- "같은 폴더 내 파일을 재노출한다"는 의도 명확

```typescript
// src/shared/ui/index.ts (권장)
export { DoughnutChart } from './DoughnutChart';
export { Flex } from './Flex';
// ...etc
```
```typescript
// 외부에서 사용할 때 (절대경로 사용)
import { DoughnutChart, Flex } from '@/shared/ui';
```

<br />

## 경로 선택 기준
### 상대경로
- 같은 폴더 내부 파일 참조
- index.ts에서 re-export, 컴포넌트 하위 파일 간 참조

### 절대경로
- 다른 폴더(모듈) 참조, 폴더 깊이가 깊은 경우, 프로젝트 전역에서 재사용
- shared, features, entities, utils 등 공통 모듈 import 시

## 추가 활용
- 배럴 패턴(Barrel Pattern): 여러 하위 모듈을 index.ts에서 한 번에 재노출
  ```typescript
  // src/shared/index.ts
  export * from './ui';
  export * from './api';
  ```
- 타입 전용 re-export
  ```typescript
  export type { User } from './User';
  ```
- 네임스페이스 객체 패턴
  ```typescript
  import { DoughnutChart } from './DoughnutChart';
  export const Charts = { Doughnut: DoughnutChart };
  ```

<br />

## 알아두면 좋은 내용
- 경로 alias(절대경로) 설정 방법(tsconfig.json의 paths 옵션, webpack alias 등)
- 순환 참조(circular dependency) 문제: re-export 구조가 복잡해질 때 의존성 그래프 주의
- ESlint 플러그인(예: import/order, import/no-relative-parent-imports)으로 코드 스타일 자동화 가능

<br />

## 연관 주제
- 배럴 패턴(Barrel Pattern) 장단점, 리팩토링 방법
- tsconfig의 `baseUrl`, `paths` 세팅과 IDE 자동완성/점프
- monorepo 구조에서의 모듈 export/import 규칙

<br />

# 🔗 References
- [TypeScript Handbook - Modules](https://www.typescriptlang.org/docs/handbook/modules.html)
- [바렐 패턴 설명 블로그](https://engineering.linecorp.com/ko/blog/barrel/)
- [ESLint import 플러그인](https://github.com/import-js/eslint-plugin-import)
