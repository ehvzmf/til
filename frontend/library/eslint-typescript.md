> 📅 Date: 2025-07-10

# 📌 Focus
- ESLint + TypeScript 연동 방법
<br />

# 📝 Learnings
> ESLint는 기본적으로 자바스크립트 린터 <br />
> 타입스크립트 문법과 타입 검사까지 하려면 별도 플러그인 필요 !
>

- 주요 패키지
  - `@typescript-eslint/parser`: 타입스크립트 코드 파싱 지원
  - `@typescript-eslint/eslint-plugin`: 타입스크립트 전용 규칙 제공
- 설치
  ```bash
  npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
  ```
- 설정
  1. ESLint 설정 파일(.eslintrc.js, .eslintrc.json 등)에서 파서와 플러그인 지정
     ```json
     {
       "parser": "@typescript-eslint/parser",
       "plugins": ["@typescript-eslint"],
       "extends": [
         "eslint:recommended",
         "plugin:@typescript-eslint/recommended"
       ],
       "rules": {
         // 필요에 따라 custom rule 추가
         "@typescript-eslint/no-unused-vars": "warn"
       }
     }
     ```
  2. 타입스크립트 환경 변수를 위한 parserOptions 추가
     ```json
     {
       "parserOptions": {
         "ecmaVersion": 2020,
         "sourceType": "module",
         "project": "./tsconfig.json"
       }
     }
     ```
  3. 타입스크립트 파일에만 별도 설정 적용 (overrides 사용)
     ```json
     {
       "overrides": [
         {
           "files": ["*.ts", "*.tsx"],
           "parser": "@typescript-eslint/parser",
           "plugins": ["@typescript-eslint"],
           "extends": [
             "plugin:@typescript-eslint/recommended"
           ]
         }
       ]
     }
     ```
- 타입 체크와 ESLint 분리
  - ESLint는 문법/스타일 위주 검사, 타입 체크는 tsc(타입스크립트 컴파일러) 사용 권장.
  - 타입 기반 린트가 필요하면 rules에 `"plugin:@typescript-eslint/recommended-requiring-type-checking"` 추가.
- 추천 워크플로우
  - `eslint . --ext .js,.ts,.tsx`
  - 타입 체크는 별도로 `tsc --noEmit` 사용.
- 장점
  - 프로젝트 내 자바스크립트와 타입스크립트 파일을 동시에 린트 가능.
  - 다양한 타입스크립트 전용 규칙으로 오류 및 스타일 통일.
  - Prettier와 같이 사용 가능(설정 분리 필요).
<br />

# 🔗 References
- [ESLint 공식 문서](https://eslint.org/docs/latest/user-guide/configuring/language-options#using-typescript)
- [@typescript-eslint 공식 문서](https://typescript-eslint.io/docs/)
