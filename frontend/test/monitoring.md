> 📅 Date: 2025-08-20

# 📌 Focus
- 모니터링 툴 붙이기
- GA4, Sentry
<br />

# 📝 Learnings
### 모니터링 툴의 목적
#### 📊 사용자 활동 분석
- 페이지별 방문 통계
- 버튼 클릭, 폼 제출 등 인터랙션
- 검색어 및 기능 사용 패턴
- 사용자 플로우 분석

#### 🚨 에러 모니터링
- JavaScript 런타임 에러
- API 호출 실패
- 컴포넌트 렌더링 에러
- 사용자별 에러 컨텍스트

#### ⚡ 성능 모니터링
- 페이지 로딩 시간
- 컴포넌트 렌더링 성능
- API 응답 시간
- Core Web Vitals (LCP, FID, CLS)
<br />

### Google Analytics 4
> 사용자 행동 분석

### Sentry
> 에러 추적

## 1. 필요한 패키지 설치

```bash
# 에러 모니터링 & 성능
npm install @sentry/react @sentry/tracing

# 사용자 활동 분석
npm install react-ga4
# 또는 더 상세한 분석을 원한다면
npm install mixpanel-browser
```

## 2. Sentry 설정 (에러 & 성능 모니터링)

### `src/lib/monitoring.ts`
```typescript
import * as Sentry from "@sentry/react";
import { BrowserTracing } from "@sentry/tracing";

export const initSentry = () => {
  Sentry.init({
    dsn: import.meta.env.VITE_SENTRY_DSN,
    integrations: [
      new BrowserTracing({
        // 자동 라우트 추적
        routingInstrumentation: Sentry.reactRouterV6Instrumentation(
          React.useEffect,
          useLocation,
          useNavigationType,
          createRoutesFromChildren,
          matchRoutes
        ),
      }),
    ],
    // 성능 모니터링 샘플링 비율
    tracesSampleRate: 1.0,
    // 환경별 설정
    environment: import.meta.env.MODE,
    beforeSend(event, hint) {
      // 개발 환경에서는 콘솔에도 출력
      if (import.meta.env.DEV) {
        console.error("Sentry Event:", event, hint);
      }
      return event;
    },
  });
};

// 커스텀 에러 리포팅
export const reportError = (error: Error, context?: Record<string, any>) => {
  Sentry.withScope((scope) => {
    if (context) {
      scope.setContext("additional_info", context);
    }
    Sentry.captureException(error);
  });
};

// 성능 측정
export const measurePerformance = (name: string, fn: () => void) => {
  const transaction = Sentry.startTransaction({ name });
  Sentry.getCurrentHub().configureScope(scope => scope.setSpan(transaction));
  
  try {
    fn();
  } finally {
    transaction.finish();
  }
};
```

## 3. Google Analytics 4 설정 (사용자 활동 분석)

### `src/lib/analytics.ts`
```typescript
import ReactGA from 'react-ga4';

export const initGA = () => {
  const trackingId = import.meta.env.VITE_GA_TRACKING_ID;
  if (trackingId) {
    ReactGA.initialize(trackingId, {
      testMode: import.meta.env.DEV, // 개발환경에서는 테스트 모드
    });
  }
};

// 페이지뷰 추적
export const trackPageView = (path: string, title?: string) => {
  ReactGA.send({ 
    hitType: "pageview", 
    page: path,
    title: title 
  });
};

// 사용자 이벤트 추적
export const trackEvent = (
  action: string,
  category: string = 'User Interaction',
  label?: string,
  value?: number
) => {
  ReactGA.event({
    action,
    category,
    label,
    value,
  });
};

// 사용자 정의 이벤트
export const trackCustomEvent = (eventName: string, parameters?: Record<string, any>) => {
  ReactGA.gtag('event', eventName, parameters);
};
```

## 4. 메인 앱에 통합

### `src/main.tsx`
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { initSentry } from './lib/monitoring';
import { initGA } from './lib/analytics';

// 모니터링 도구 초기화
initSentry();
initGA();

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

## 5. 커스텀 훅으로 사용자 활동 추적

### `src/hooks/useTracking.ts`
```typescript
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';
import { trackPageView, trackEvent } from '../lib/analytics';

export const usePageTracking = () => {
  const location = useLocation();

  useEffect(() => {
    trackPageView(location.pathname + location.search);
  }, [location]);
};

export const useUserTracking = () => {
  const trackClick = (elementId: string, elementType?: string) => {
    trackEvent('click', 'UI Interaction', `${elementType || 'button'}_${elementId}`);
  };

  const trackFormSubmit = (formName: string, success: boolean) => {
    trackEvent(
      success ? 'form_submit_success' : 'form_submit_error',
      'Form Interaction',
      formName
    );
  };

  const trackSearch = (searchTerm: string, resultsCount?: number) => {
    trackEvent('search', 'Search', searchTerm, resultsCount);
  };

  const trackFeatureUsage = (featureName: string) => {
    trackEvent('feature_usage', 'Feature', featureName);
  };

  return {
    trackClick,
    trackFormSubmit,
    trackSearch,
    trackFeatureUsage,
  };
};
```

## 6. 성능 모니터링 컴포넌트

### `src/components/PerformanceMonitor.tsx`
```typescript
import React, { useEffect } from 'react';
import * as Sentry from '@sentry/react';

interface PerformanceMonitorProps {
  name: string;
  children: React.ReactNode;
}

export const PerformanceMonitor: React.FC<PerformanceMonitorProps> = ({ 
  name, 
  children 
}) => {
  useEffect(() => {
    const transaction = Sentry.startTransaction({ name: `Component: ${name}` });
    Sentry.getCurrentHub().configureScope(scope => scope.setSpan(transaction));

    return () => {
      transaction.finish();
    };
  }, [name]);

  return <>{children}</>;
};

// 사용 예시
export const Dashboard = () => {
  return (
    <PerformanceMonitor name="Dashboard">
      <div>대시보드 내용</div>
    </PerformanceMonitor>
  );
};
```

## 7. 에러 바운더리

### `src/components/ErrorBoundary.tsx`
```typescript
import React from 'react';
import * as Sentry from '@sentry/react';

const ErrorBoundary = Sentry.withErrorBoundary(
  ({ children }: { children: React.ReactNode }) => <>{children}</>,
  {
    fallback: ({ error, resetError }) => (
      <div className="error-boundary">
        <h2>문제가 발생했습니다</h2>
        <details style={{ whiteSpace: 'pre-wrap' }}>
          {error && error.toString()}
        </details>
        <button onClick={resetError}>다시 시도</button>
      </div>
    ),
    beforeCapture: (scope, error, errorInfo) => {
      scope.setTag('errorBoundary', true);
      scope.setContext('errorInfo', errorInfo);
    },
  }
);

export default ErrorBoundary;
```

## 8. 환경 변수 설정

### `.env`
```bash
# Sentry
VITE_SENTRY_DSN=https://your-sentry-dsn@sentry.io/project-id

# Google Analytics
VITE_GA_TRACKING_ID=G-XXXXXXXXXX
```

## 9. 실제 사용 예시

### `src/components/UserDashboard.tsx`
```typescript
import React from 'react';
import { usePageTracking, useUserTracking } from '../hooks/useTracking';
import { reportError } from '../lib/monitoring';

export const UserDashboard: React.FC = () => {
  usePageTracking(); // 자동 페이지뷰 추적
  const { trackClick, trackFeatureUsage } = useUserTracking();

  const handleButtonClick = async () => {
    trackClick('export-button', 'dashboard');
    
    try {
      trackFeatureUsage('data_export');
      // API 호출 등
      await exportData();
    } catch (error) {
      reportError(error as Error, {
        feature: 'data_export',
        userAction: 'button_click'
      });
    }
  };

  return (
    <div>
      <button onClick={handleButtonClick}>
        데이터 내보내기
      </button>
    </div>
  );
};
```

## 10. 추가 설정 팁

### Vite 설정에서 소스맵 활성화 (`vite.config.ts`)
```typescript
export default defineConfig({
  build: {
    sourcemap: true, // Sentry에서 정확한 에러 위치 파악을 위해
  },
  define: {
    __SENTRY_DEBUG__: false,
  },
});
```
