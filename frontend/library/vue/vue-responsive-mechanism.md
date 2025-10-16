> 📅 Date: 2025-10-16

# 📌 Focus
- Vue의 반응성 시스템 동작 원리
- Proxy vs Object.defineProperty
- ref/reactive 차이
<br />

# 📝 Learnings

## Vue 2 vs Vue 3 반응성 시스템 비교

### Vue 2: Object.defineProperty 기반

#### 동작 방식
```javascript
// Vue 2 내부 구현 방식 (단순화)
function observe(obj) {
  Object.keys(obj).forEach(key => {
    let value = obj[key]
    Object.defineProperty(obj, key, {
      get() {
        // 의존성 수집 (dependency tracking)
        track(obj, key)
        return value
      },
      set(newValue) {
        value = newValue
        // 변경 감지 시 업데이트 트리거
        trigger(obj, key)
      }
    })
  })
}
```

#### 한계점
- **배열 인덱스 감지 불가**: `arr[0] = newValue` 감지 못함
- **새로운 속성 추가 감지 불가**: `obj.newProp = value` 감지 못함
- **객체/배열 삭제 감지 불가**: `delete obj.prop` 감지 못함
- **성능 이슈**: 모든 속성을 재귀적으로 처리해야 함

### Vue 3: Proxy 기반

#### 동작 방식
```javascript
// Vue 3 내부 구현 방식 (단순화)
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      const result = Reflect.get(target, key, receiver)
      // 의존성 수집
      track(target, key)
      return result
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver)
      // 변경 감지 시 업데이트 트리거
      trigger(target, key)
      return result
    },
    deleteProperty(target, key) {
      const result = Reflect.deleteProperty(target, key)
      trigger(target, key)
      return result
    }
  })
}
```

#### 장점
- **모든 연산 감지**: 속성 추가/삭제, 배열 인덱스 변경 등 모든 변화 감지
- **중첩 객체 지연 처리**: 필요할 때만 중첩 객체를 반응형으로 변환
- **성능 향상**: 초기 설정 시 모든 속성을 순회하지 않음
- **더 넓은 지원**: Map, Set, WeakMap, WeakSet 등도 지원

## ref vs reactive 내부 구현 차이

### ref의 구현 원리

```javascript
// ref 내부 구현 (단순화)
class RefImpl {
  constructor(value) {
    this._value = convert(value) // 객체면 reactive()로 변환
  }
  
  get value() {
    track(this, 'value')
    return this._value
  }
  
  set value(newValue) {
    this._value = convert(newValue)
    trigger(this, 'value')
  }
}

function ref(value) {
  return new RefImpl(value)
}

function convert(value) {
  return isObject(value) ? reactive(value) : value
}
```

#### ref의 특징
- **단일 값 래핑**: 원시값을 객체로 래핑해서 반응성 제공
- **`.value` 접근**: 템플릿에서는 자동 언래핑, 스크립트에서는 `.value` 필요
- **중첩 객체 자동 변환**: 객체를 할당하면 자동으로 `reactive()`로 변환

### reactive의 구현 원리

```javascript
// reactive 내부 구현 (단순화)
const reactiveMap = new WeakMap()

function reactive(target) {
  // 이미 반응형 객체면 그대로 반환
  if (reactiveMap.has(target)) {
    return reactiveMap.get(target)
  }
  
  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      const result = Reflect.get(target, key, receiver)
      track(target, key)
      
      // 중첩 객체도 반응형으로 변환 (지연 처리)
      if (isObject(result)) {
        return reactive(result)
      }
      return result
    },
    set(target, key, value, receiver) {
      const oldValue = target[key]
      const result = Reflect.set(target, key, value, receiver)
      
      if (oldValue !== value) {
        trigger(target, key)
      }
      return result
    }
  })
  
  reactiveMap.set(target, proxy)
  return proxy
}
```

#### reactive의 특징
- **객체 전체 반응형**: 객체의 모든 속성이 반응형
- **중첩 구조 지원**: 깊은 중첩도 자동으로 반응형 변환
- **직접 접근**: `.value` 없이 직접 속성 접근

## 반응성 손실이 일어나는 경우들

### 1. 구조 분해 할당 (Destructuring)

```javascript
// ❌ 반응성 손실
const state = reactive({ count: 0, name: 'Vue' })
const { count } = state // count는 더이상 반응형이 아님

// ✅ 반응성 유지
const { count } = toRefs(state) // toRefs로 래핑
// 또는
const count = toRef(state, 'count') // 개별 속성만
```

### 2. 함수 매개변수로 전달

```javascript
// ❌ 반응성 손실
const state = reactive({ count: 0 })
function logCount(count) {
  console.log(count) // 원시값만 전달됨
}
logCount(state.count)

// ✅ 반응성 유지
function logCount(state) {
  console.log(state.count) // 객체 전체 전달
}
logCount(state)
```

### 3. ref의 재할당

```javascript
// ❌ 반응성 손실
let count = ref(0)
count = ref(10) // 새로운 ref 객체 생성, 기존 반응성 끊어짐

// ✅ 반응성 유지
let count = ref(0)
count.value = 10 // 같은 ref 객체의 값만 변경
```

### 4. 배열 메서드 체이닝

```javascript
// ❌ 반응성 손실 가능
const numbers = ref([1, 2, 3])
const doubled = numbers.value.map(n => n * 2) // 새 배열 생성

// ✅ 반응성 유지
const doubled = computed(() => numbers.value.map(n => n * 2))
```

## watch vs watchEffect 사용 시나리오

### watch: 명시적 의존성 관리

```javascript
// 특정 소스 감시
const count = ref(0)
const name = ref('Vue')

// 단일 소스 감시
watch(count, (newCount, oldCount) => {
  console.log(`Count changed from ${oldCount} to ${newCount}`)
})

// 다중 소스 감시
watch([count, name], ([newCount, newName], [oldCount, oldName]) => {
  console.log('Multiple values changed')
})

// 깊은 감시
const state = reactive({ nested: { count: 0 } })
watch(() => state.nested, (newNested) => {
  console.log('Nested object changed')
}, { deep: true })

// 즉시 실행
watch(count, (value) => {
  console.log('Initial and changed:', value)
}, { immediate: true })
```

#### watch 사용 시나리오
- **특정 값 변화만 관심있을 때**
- **이전 값과 비교가 필요할 때**
- **조건부 사이드 이펙트 실행**
- **debouncing/throttling 적용**

### watchEffect: 자동 의존성 수집

```javascript
// 자동으로 의존성 수집
const count = ref(0)
const doubled = ref(0)

watchEffect(() => {
  // count가 변경될 때마다 자동 실행
  doubled.value = count.value * 2
  console.log(`Count: ${count.value}, Doubled: ${doubled.value}`)
})

// 비동기 정리
watchEffect((onInvalidate) => {
  const controller = new AbortController()
  
  fetch('/api/data', { signal: controller.signal })
    .then(response => response.json())
    .then(data => {
      // 데이터 처리
    })
  
  // 컴포넌트 언마운트시 또는 재실행시 정리
  onInvalidate(() => {
    controller.abort()
  })
})

// 실행 타이밍 제어
watchEffect(() => {
  // DOM 업데이트 후 실행
}, { flush: 'post' })
```

#### watchEffect 사용 시나리오
- **여러 반응형 값을 조합할 때**
- **의존성이 동적으로 변할 때**
- **사이드 이펙트가 간단할 때**
- **API 호출 등 비동기 작업**

## 성능 최적화 팁

### 1. 불필요한 반응성 회피
```javascript
// ❌ 모든 속성이 반응형
const config = reactive({
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
})

// ✅ 상수는 일반 객체로
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
}
```

### 2. shallowRef/shallowReactive 활용
```javascript
// 깊은 중첩이 불필요한 경우
const largeData = shallowRef({
  // 큰 데이터 구조
})

// 최상위 레벨만 반응형
const cache = shallowReactive(new Map())
```
<br />

# 🔗 References
- [Vue 3 Reactivity in Depth](https://vuejs.org/guide/extras/reactivity-in-depth.html)
- [Vue 3 Reactivity Transform RFC](https://github.com/vuejs/rfcs/discussions/369)
- [Evan You - Vue 3 Reactivity 설명 영상](https://www.youtube.com/watch?v=NZfNS4sJ8CI)
- [Vue 3 Source Code - Reactivity Package](https://github.com/vuejs/core/tree/main/packages/reactivity)
- [MDN - Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
- [Vue 3 Migration Guide - Reactivity](https://v3-migration.vuejs.org/breaking-changes/reactivity-api.html)
