> 📆 2025-07-15
>

# ⚠️ ISSUE
- cicd 환경에서 퍼블리싱 중 오류 발생
- 빌드 중 rollup 에러가 발생한 것을 확인하고 디버깅

<details>
<summary>🚨 빌드 중 rollup 에러 발생</summary>

```cmd
✗ Build failed in 5.48s
error during build:
Cannot add property 0, object is not extensible
    at Array.push (<anonymous>)
    at ConditionalExpression.getLiteralValueAtPath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:12270:45)
    at file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:5064:30
    at EntityPathTracker.withTrackedEntityAtPath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:2014:24)
    at LocalVariable.getLiteralValueAtPath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:5062:33)
    at Identifier.getLiteralValueAtPath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:5207:48)
    at ObjectExpression.getObjectEntity (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:14262:47)
    at ObjectExpression.deoptimizePath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:14201:14)
    at CallExpression.deoptimizePath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:11929:30)
    at Property.deoptimizePath (file:///C:/Users/user/Desktop/coup_frontend/node_modules/.pnpm/rollup@4.45.0/node_modules/rollup/dist/es/shared/node-entry.js:5667:36)
 ELIFECYCLE  Command failed with exit code 1.
```
</details>

<br />

### 📌 구체적인 에러
> Rollup 4.45.0에서 발생하는 내부 오류 <br />
> 빌드 과정에서 객체가 확장 불가능한 상태에서 배열에 요소를 추가하려고 시도<br />
> getLiteralValueAtPath 관련 오류로 코드 분석 중 발생
> 
- 로컬 환경에서는 문제되지 않았지만, 매번 의존성을 새로 설치하는 cicd 환경에서 문제가 발생하여


# 🛠️ 해결해보자! 
### 1. 최근 추가된 파일 삭제 후 다시 빌드
- cicd가 잘 동작하다가 최근 pr에서 실패했으므로 빌드 오류가 발생할 여지가 있다고 파악했다.
- 처음에는 rollup 오류가 구조 분해, 임포트 등에서도 발생할 수 있다고 해서 코드만 수정했으나 효과 x
- 마찬가지로 파일을 아예 제외해도 같은 문제가 지속됐다. 

### 2. pnpm 대신 npm으로 의존성 설치 및 빌드
- 로컬 환경에서는 pnpm을 사용했으나 cicd 환경에서는 npm 명령어를 사용
- pnpm.lock.yml과 package-lock.json의 차이가 문제일 수도 있다고 파악
- 혹은 로컬에서는 오래된 캐시를 사용해서 괜찮았으나, 배포 환경에서는 새로 의존성을 설치하면서 버전 오류가 발생할 수 있다.
- 기존 의존성과 캐시를 삭제 후 npm으로 다시 설치 후 빌드 -> 배포 환경과 같은 문제 발생
- 의존성 캐시로 인해 문제를 진작 파악하지 못하고 있었던 것.

### 3. vite + rollup 다운그레이드
- vite 6.24.x -> 내장 rollup 4.45.0
- 최근 버전이고 안정성이 부족할 수 있다는 보고가 있어 다운그레이드
- vite 5.x, rollup 4.3.x
- 하지만 계속 같은 문제 발생

### 4. node 다운그레이드
- 코파일럿의 도움으로 node LTS 버전이 아니라서 그럴 수 있다는 조언으로, 다운그레이드 시도
- 22는 LTS임... 인공지능의 오래된 정보를 맹신하지 말고 찾아보자.
- 롤백

# 최종 해결
vite.config.ts에 rollup 설정 우회
```ts

```
