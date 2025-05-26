> 📅 **Date**: 2025-05-22

## 📌 Focus

🎭 Playwright 

<br />

Microsoft에서 개발한 오픈소스 **E2E(End-to-End) 테스트 프레임워크**<br />
브라우저를 자동으로 조작하여 실제 사용자처럼 웹 애플리케이션을 테스트 <br />
**크로스 브라우징**, **자동 대기**, **API 모킹**, **스토리지 상태 유지**<br />

<br />

## 🧠 Playwright의 핵심 개념

### 1. 브라우저(Browser)

* Playwright는 Chromium, Firefox, WebKit 등 다양한 브라우저를 지원한다.
* 각 브라우저 인스턴스를 생성하여 테스트를 실행한다.

<br />

### 2. 브라우저 컨텍스트(Browser Context)

* 브라우저 내에서 독립적인 세션을 생성한다.
* 각 테스트는 새로운 컨텍스트에서 실행되어 서로 영향을 주지 않는다.

<br />

### 3. 페이지(Page)

* 실제 브라우저의 탭과 유사한 개념
* 페이지를 통해 웹사이트를 탐색하고, 요소와 상호작용

<br />

### 4. 선택자(Selectors)

* CSS, XPath, 텍스트 등 다양한 방법으로 요소를 선택 가능
* `getByRole`, `getByText` 등 접근성 친화적인 선택자도 지원

<br />

### 5. 자동 대기(Auto-waiting)

* Playwright는 요소가 나타날 때까지 자동으로 대기
* 명시적인 `wait` 없이도 안정적인 테스트가 가능

<br />

<br />

## 🛠️ 설치 및 프로젝트 초기화

### 1. Node.js 설치

* Playwright는 Node.js 환경에서 동작

<br />

### 2. Playwright 설치

```bash
npm init playwright@latest
```

* 옵션: 

  * TypeScript 또는 JavaScript 선택
  * 테스트 디렉토리 설정
  * GitHub Actions 워크플로우 설정 여부
  * 브라우저 설치 여부

<br />

### 3. 프로젝트 구조

* 기본 구조: 

```
project/
├── tests/
│   └── example.spec.ts
├── playwright.config.ts
├── package.json
└── ...
```

* `tests/`: 테스트 파일을 저장하는 디렉토리
* `playwright.config.ts`: Playwright 설정 파일

<br />

## ✍️ 첫 번째 테스트 작성

```typescript
import { test, expect } from '@playwright/test';

test('홈페이지 타이틀 확인', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle('Example Domain');
});
```

* `test`: 테스트 케이스를 정의
* `page.goto`: 지정한 URL로 이동
* `expect`: 예상 결과를 검증

<br />

## 🚀 테스트 실행

### 1. 모든 테스트 실행

```bash
npx playwright test
```

* `tests/` 디렉토리의 모든 테스트를 실행

### 2. 특정 테스트 실행

```bash
npx playwright test tests/example.spec.ts
```

* 지정한 테스트 파일만 실행

### 3. UI 모드 실행

```bash
npx playwright test --ui
```

* 테스트를 시각적으로 확인하고 디버깅할 수 있는 UI를 제공

<br />

## 🧪 고급 기능

### 1. 스토리지 상태 유지

* 로그인 상태를 유지하여 매 테스트마다 로그인 과정을 생략 가능 

```typescript
// global-setup.ts
import { chromium } from '@playwright/test';

async function globalSetup() {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com/login');
  await page.fill('#username', 'user');
  await page.fill('#password', 'pass');
  await page.click('text=Login');
  await page.context().storageState({ path: 'storageState.json' });
  await browser.close();
}

export default globalSetup;
```

* `playwright.config.ts`에 설정 추가:

```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  globalSetup: require.resolve('./global-setup'),
  use: {
    storageState: 'storageState.json',
  },
});
```

### 2. API 모킹

* 외부 API 호출을 모킹하여 테스트의 안정성 향상 

```typescript
test('API 모킹 예제', async ({ page }) => {
  await page.route('**/api/data', async route => {
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({ data: 'mocked data' }),
    });
  });

  await page.goto('https://example.com');
  // 테스트 로직...
});
```

<br />

## 📊 테스트 리포트

* 테스트 실행 후 HTML 리포트를 생성하여 결과를 시각적으로 확인 

```bash
npx playwright show-report
```

* 리포트에는 테스트 결과, 스크린샷, 비디오 등이 포함

<br />

## 🔍 디버깅 팁

* `--debug` 플래그를 사용하여 디버깅 모드로 실행 가능 

```bash
npx playwright test --debug
```

* `page.pause()`를 코드에 삽입하여 특정 지점에서 실행을 일시 중지 가능 

<br />

## 📚 추가 학습 자료

* [Playwright 공식 문서](https://playwright.dev/)
* [Playwright GitHub 저장소](https://github.com/microsoft/playwright)
* [Playwright 예제 모음](https://github.com/microsoft/playwright/tree/main/examples)
