> 📅 Date: 2025-07-29

# 📌 Focus
- useIsFetching Hook
- React Query (TanStack Query)
- Global Loading State Management
<br />

# 📝 Learnings
## useIsFetching
- 애플리케이션에서 현재 로딩 중이거나 백그라운드에서 페칭 중인 쿼리의 수를 반환하는 옵셔널 훅 <br />
- 앱 전체의 로딩 인디케이터를 구현할 수 있다. 

```javascript
import { useIsFetching } from '@tanstack/react-query'

// 전체 쿼리 중 페칭 중인 개수
const isFetching = useIsFetching()

// 특정 queryKey로 필터링
const isFetchingPosts = useIsFetching({ queryKey: ['posts'] })
```

## 앱 최적화를 위한 중앙화 전략

### 1. 전역 로딩 인디케이터 구현
전역 로딩 인디케이터를 위해 useIsFetching 훅을 사용하여 백그라운드를 포함한 모든 쿼리가 페칭 중일 때 표시

```javascript
import { useIsFetching } from '@tanstack/react-query'

function GlobalLoadingIndicator() {
  const isFetching = useIsFetching()
  
  return isFetching ? (
    <div className="global-loading">
      Queries are fetching in the background...
    </div>
  ) : null
}
```

### 2. Context를 활용한 중앙 상태 관리
useIsFetching과 useIsMutating 훅을 Context 프로바이더와 결합하여 깊이 중첩된 컴포넌트 트리에서 효율적으로 상태를 관리하는 방법 <br />
중앙화된 페칭 및 뮤테이션 상태 추적이 가능하다. 

```javascript
// 통합 서비스 상태 관리 훅
const useServiceStatus = (mode = 'all') => {
  const isFetching = useIsFetching()
  const isMutating = useIsMutating()
  const isRestoring = useIsRestoring()
  
  return useMemo(() => {
    switch (mode) {
      case 'fetching': return isFetching > 0
      case 'mutating': return isMutating > 0
      case 'restoring': return isRestoring
      default: return isFetching > 0 || isMutating > 0 || isRestoring
    }
  }, [isFetching, isMutating, isRestoring, mode])
}
```

### 3. 필터링을 통한 세밀한 최적화
특정 쿼리나 조건에 따른 필터링으로 불필요한 리렌더링 방지

```javascript
// predicate를 사용한 필터링
const isFetching = useIsFetching({ 
  predicate: query => query.queryKey.at(1) === 'key2' 
})

// 특정 상태의 쿼리만 감지
const isLoadingGlobally = useIsFetching({ 
  predicate: query => query.state.status === 'loading' 
})
```

### 4. 성능 최적화 팁
- React Query는 구조적 공유(structural sharing)와 변경 감지 최적화를 통해 불필요한 리렌더링을 자동으로 방지
- useIsFetching은 실제로 사용되는 속성만 변경될 때만 리렌더링을 트리거
- 전역 로딩 상태를 관리할 때는 debounce나 최소 표시 시간을 설정하여 깜빡임 현상을 방지하는 게 좋다. 

```javascript
// 100ms 지연으로 깜빡임 방지
const useDelayedLoading = () => {
  const isFetching = useIsFetching()
  const [showLoading, setShowLoading] = useState(false)
  
  useEffect(() => {
    let timer
    if (isFetching) {
      timer = setTimeout(() => setShowLoading(true), 100)
    } else {
      setShowLoading(false)
    }
    return () => clearTimeout(timer)
  }, [isFetching])
  
  return showLoading
}
```
<br />
# 🔗 References
- [TanStack Query useIsFetching 공식 문서](https://tanstack.com/query/latest/docs/framework/react/reference/useIsFetching)
- [Background Fetching Indicators](https://tanstack.com/query/v4/docs/framework/react/guides/background-fetching-indicators)
- [React Query Render Optimizations](https://tanstack.com/query/latest/docs/framework/react/guides/render-optimizations)
