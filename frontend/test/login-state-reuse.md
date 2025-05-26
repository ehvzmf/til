> 📅 Date: 2025-05-26

<br /> 

# 📌 Focus

Playwright에서 세션 스토리지를 활용해 로그인 상태를 재사용하기
(SNS 소셜 로그인 포함)

<br /> 

# 📝 Learnings

> Playwright는 테스트를 실행할 때마다 새 브라우저 컨텍스트에서 시작 <br /> 
> 이때 로그인 상태가 유지되지 않아 매 테스트마다 다시 로그인해야 하는 이슈 발생 <br /> 
> 특히 소셜 로그인은 자동화가 어렵고 로컬에서 오류가 잦기 때문에 <br /> 
> 한 번 로그인한 상태를 저장해 테스트에 재활용한다. 

<br /> 

## `storageState`

Playwright는 로그인 후의 인증 상태(세션, 쿠키, localStorage 등)를 `.json` 파일로 저장하고,
이 상태를 이후 테스트에서 재사용할 수 있다. 

<br /> 

## ✅ 설정 흐름

### 1️⃣ 로그인 상태 저장 (Global Setup)

```ts
// global-setup.ts
import { chromium } from '@playwright/test';

async function globalSetup() {
  const browser = await chromium.launch();
  const page = await browser.newPage();

  await page.goto('https://your-app.com/login');

  // 👉 소셜 로그인 직접 수행
  await page.click('text=카카오 로그인');
  await page.fill('#email', 'your@email.com');
  await page.fill('#password', 'your_password');
  await page.click('button[type=submit]');

  // ✅ 로그인 후 스토리지 상태 저장
  await page.context().storageState({ path: 'storageState.json' });
  await browser.close();
}

export default globalSetup;
```

> 실제로 카카오/구글 등 OAuth 로그인 과정을 거치고
> 로그인 후의 쿠키/로컬스토리지 등을 `storageState.json`에 저장

<br /> 

### 2️⃣ 설정 파일에 적용

```ts
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  globalSetup: require.resolve('./global-setup'),
  use: {
    storageState: 'storageState.json',
  },
});
```

* 이후 테스트에서는 `로그인 없이 자동으로 인증된 상태`로 시작

<br /> 

### 3️⃣ 실제 테스트 예시

```ts
import { test, expect } from '@playwright/test';

test('로그인 후 접근 가능한 페이지 확인', async ({ page }) => {
  await page.goto('https://your-app.com/dashboard');
  await expect(page.locator('text=로그아웃')).toBeVisible(); // 로그인된 상태
});
```

<br /> 

## ✅ 장점

| 항목   | 설명                             |
| ---- | ------------------------------ |
| 속도   | 매번 로그인하는 시간 절약 (특히 소셜 로그인은 느림) |
| 안정성  | 로그인 실패로 인한 테스트 실패 방지           |
| 유지보수 | 로그인 테스트는 별도 수행, 각 테스트는 목적에 집중  |

<br /> 

## 🧠 실무 팁

* `storageState.json`은 민감 정보가 담기므로 **버전관리(Git)에 포함 X**
* 주기적으로 로그인 상태를 재생성 (`npm run auth:setup` 등 커맨드 구성)
* SNS 로그인은 2단계 인증 등으로 막힐 수 있어 **테스트 전용 계정 권장**

<br /> 

## ✅ 요약

| 항목 | 설명                                                               |
| -- | ---------------------------------------------------------------- |
| 문제 | 매 테스트마다 로그인 필요, 특히 소셜 로그인은 자동화 어려움                               |
| 해결 | 한 번 로그인 후 `storageState` 저장 → 이후 테스트 재사용                         |
| 구성 | `global-setup.ts` + `storageState.json` + `playwright.config.ts` |
| 활용 | 모든 테스트에서 로그인 과정을 생략하고 인증된 상태로 시작 가능                              |
