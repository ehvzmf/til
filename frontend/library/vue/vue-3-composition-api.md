> 📅 Date: 2025-10-15

# 📌 Focus
- Vue 3 Composition API 설계 철학
<br />

# 📝 Learnings

## Vue 3 Composition API 등장 배경

### Options API의 한계점
- **로직 분산 문제**: 하나의 기능이 data, methods, computed, watch 등 여러 옵션에 흩어짐
- **재사용성 부족**: 컴포넌트 간 로직 공유가 어려움 (mixins의 네이밍 충돌, 암묵적 의존성)
- **TypeScript 지원 한계**: this 컨텍스트로 인한 타입 추론의 어려움
- **대형 컴포넌트 관리**: 코드가 길어질수록 관련 로직을 찾기 어려움

### Composition API의 핵심 설계 원칙

#### 1. **Logical Concern 기반 구성**
```javascript
// Options API - 로직이 흩어짐
export default {
  data() {
    return { count: 0, user: null }
  },
  methods: {
    increment() { this.count++ },
    fetchUser() { /* ... */ }
  },
  computed: {
    doubleCount() { return this.count * 2 }
  }
}

// Composition API - 관련 로직이 함께
export default {
  setup() {
    // 카운터 관련 로직
    const count = ref(0)
    const doubleCount = computed(() => count.value * 2)
    const increment = () => count.value++

    // 사용자 관련 로직
    const user = ref(null)
    const fetchUser = async () => { /* ... */ }

    return { count, doubleCount, increment, user, fetchUser }
  }
}
```

#### 2. **함수형 프로그래밍 패러다임**
- **순수 함수**: 사이드 이펙트 최소화
- **조합 가능성**: 작은 함수들을 조합해서 복잡한 로직 구성
- **명시적 의존성**: 무엇을 사용하는지 명확하게 표현

#### 3. **Tree-shaking 친화적**
- 사용하지 않는 기능은 번들에서 제외
- 필요한 기능만 import해서 사용

## Composition API vs React Hooks 철학적 차이

### 공통점
- 함수형 컴포넌트 지향
- 로직 재사용성 개선
- 상태 로직의 조합 가능성

### 차이점

#### **반응성 시스템**
```javascript
// Vue Composition API - 명시적 반응성
const count = ref(0)
const doubled = computed(() => count.value * 2)
// count가 변경되면 doubled도 자동 업데이트

// React Hooks - 의존성 배열 관리
const [count, setCount] = useState(0)
const doubled = useMemo(() => count * 2, [count])
// 의존성 배열을 직접 관리해야 함
```

#### **실행 모델**
- **Vue**: setup 함수는 컴포넌트 생성 시 한 번만 실행
- **React**: 함수 컴포넌트는 매 렌더링마다 실행

#### **상태 관리 방식**
- **Vue**: Proxy 기반 자동 반응성
- **React**: 명시적 setState 호출

## Composition API 도입 전략

### 1. **점진적 도입 가능**
```javascript
// 기존 Options API와 함께 사용 가능
export default {
  data() {
    return { legacy: 'data' }
  },
  setup() {
    const modern = ref('reactive data')
    return { modern }
  }
}
```

### 2. **Composables 패턴**
```javascript
// 재사용 가능한 로직 추출
function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  const increment = () => count.value++
  const decrement = () => count.value--
  return { count, increment, decrement }
}

// 여러 컴포넌트에서 재사용
const { count, increment } = useCounter(10)
```

### 3. **복잡성에 따른 선택**
- **간단한 컴포넌트**: Options API도 충분
- **복잡한 로직**: Composition API 권장
- **로직 재사용 필요**: Composables 패턴 활용

## 실무 관점에서의 장점

### 개발 경험 개선
- **IDE 지원**: 더 나은 자동완성과 타입 체크
- **디버깅**: 로직의 흐름을 따라가기 쉬움
- **코드 구조**: 기능별로 코드를 그룹화

### 유지보수성
- **관심사 분리**: 관련된 로직을 한 곳에 모음
- **테스트 용이성**: 독립적인 함수들로 구성
- **코드 분할**: 큰 컴포넌트를 작은 composables로 분리

### 성능
- **번들 크기**: 사용하지 않는 기능 제거
- **런타임 성능**: 불필요한 객체 생성 최소화
<br />

# 🔗 References
- [Vue 3 Composition API RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0013-composition-api.md)
- [Evan You - Vue 3 Composition API 설명 영상](https://www.youtube.com/watch?v=bwItFdPt-6M)
- [Vue 3 공식 문서 - Composition API FAQ](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Vue 3 Composition API vs React Hooks 비교](https://blog.logrocket.com/vue-composition-api-vs-react-hooks/)
- [Composables 패턴 가이드](https://vuejs.org/guide/reusability/composables.html)
- [Anthony Fu의 VueUse 라이브러리](https://vueuse.org/) - Composables 실전 예제

# 💭 Thoughts
- Options API도 여전히 유용하지만, 복잡한 로직을 다룰 때는 Composition API의 장점이 명확함
- React Hooks와 비슷해 보이지만 Vue의 반응성 시스템 덕분에 더 직관적인 면이 있음
- Composables 패턴이 특히 매력적 - 로직을 라이브러리처럼 재사용할 수 있어서 DRY 원칙을 지키기 좋음
- 점진적 도입이 가능하다는 점이 실무에서 큰 장점
