> 📅 Date: 2025-11-13

# 📌 Focus
- React Error Boundary
- 런타임 에러 처리
- 애플리케이션 안정성
<br />

# 📝 Learnings

## ErrorBoundary란?
React 컴포넌트 트리에서 발생하는 **런타임 JavaScript 에러**를 잡아 전체 앱이 크래시되는 것을 방지하는 컴포넌트.

## 도입 배경
API 에러는 TanStack Query의 `onError`로 처리 가능하지만, **"Cannot read properties of null (reading 'law')"** 같은 **예상치 못한 런타임 에러**는 대응할 방법이 없었음.
- ❌ 기존: 흰 화면 + 콘솔 에러 → 사용자 경험 최악
- ✅ 개선: 에러 발생 시에도 친화적인 UI 표시 + 복구 옵션 제공

## 구현 내용

### 1. ErrorBoundary 컴포넌트 (`src/shared/ui/ErrorBoundary.tsx`)
```typescript
/**
 * ErrorBoundary
 * 
 * React 컴포넌트 트리에서 발생하는 런타임 에러를 잡아 UI 크래시를 방지하는 에러 경계 컴포넌트.
 * 
 * @note
 * - 이벤트 핸들러, 비동기 코드 내부 에러는 잡지 못함 (try-catch 사용 필요)
 * - 에러 로깅이 필요한 경우 componentDidCatch에서 Sentry 등과 연동
 */

interface Props {
  children: ReactNode;
  fallback?: ReactNode; // 에러 발생 시 표시할 커스텀 UI
}

export class ErrorBoundary extends Component<Props, State> {
  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: any) {
    console.error('ErrorBoundary caught:', error, errorInfo);
    // TODO: Sentry 등 에러 로깅 서비스 연동
  }
  
  render() {
    if (this.state.hasError) {
      return this.props.fallback || <DefaultErrorUI />;
    }
    return this.props.children;
  }
}
```

### 2. 적용 위치 (`src/app/router.tsx`)
최상위 레벨에서 전체 앱을 보호:
```typescript
export const router = createBrowserRouter([
  {
    path: '/',
    element: (
      <ErrorBoundary>
        <Layout />
      </ErrorBoundary>
    ),
    children: [...]
  }
]);
```

## 핵심 개념

### ErrorBoundary가 잡는 에러
- ✅ 렌더링 중 발생한 에러
- ✅ 생명주기 메서드 내 에러
- ✅ 자식 컴포넌트의 constructor 에러

### ErrorBoundary가 잡지 못하는 에러
- ❌ 이벤트 핸들러 (`onClick` 등) → `try-catch` 사용
- ❌ 비동기 코드 (`setTimeout`, `Promise`) → `try-catch` 또는 `.catch()` 사용
- ❌ 서버 사이드 렌더링
- ❌ ErrorBoundary 자체의 에러

## 효과
1. **사용자 경험 개선**: 에러 발생 시에도 "문제가 발생했습니다" UI 표시 + 새로고침 버튼 제공
2. **앱 안정성 향상**: 특정 컴포넌트 에러가 전체 앱을 다운시키지 않음
3. **디버깅 용이**: 콘솔 로그 + 향후 Sentry 연동으로 에러 추적 가능
4. **null 참조 에러 대응**: `activity.law` 같은 null 참조 에러 발생 시에도 앱 유지

## 추가 개선 가능 사항
- [ ] Sentry 같은 에러 모니터링 서비스 연동
- [ ] 페이지별/컴포넌트별 세밀한 ErrorBoundary 적용
- [ ] 에러 타입에 따른 맞춤형 Fallback UI
- [ ] 에러 발생 시 자동 재시도 로직

<br />

# 🔗 References
- [React Error Boundaries 공식 문서](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)
- [Error Boundaries in React 18](https://react.dev/blog/2022/03/29/react-v18#new-strict-mode-behaviors)
