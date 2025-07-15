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

