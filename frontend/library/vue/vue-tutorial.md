> 📅 Date: 2025-10-07

# 📌 Focus
- Vue 3 프로젝트 생성
- 기본 명령어 모음 
<br />

# 📝 Learnings

## 1. 프로젝트 생성 및 설정

### Vue 3 프로젝트 생성
```bash
# 새 프로젝트 생성
npm create vue@latest my-vue-app

# 프로젝트 폴더로 이동
cd my-vue-app

# 의존성 설치
npm install

# 개발 서버 실행
npm run dev
```

### 프로젝트 생성 시 선택 옵션들
- TypeScript: Yes/No (타입스크립트 사용 여부)
- JSX: No (보통 사용 안함)
- Vue Router: Yes (페이지 라우팅)
- Pinia: Yes (상태 관리)
- Vitest: Yes (테스팅)
- ESLint: Yes (코드 린팅)
- Prettier: Yes (코드 포맷팅)

## 2. 기본 명령어

```bash
# 개발 서버 실행
npm run dev

# 프로덕션 빌드
npm run build

# 빌드 결과물 미리보기
npm run preview

# 린팅 검사
npm run lint

# 린팅 자동 수정
npm run lint:fix

# 테스트 실행
npm run test
```

## 3. Vue 3 기본 문법

### 컴포넌트 기본 구조
```vue
<template>
  <div class="hello">
    <h1>{{ title }}</h1>
    <button @click="increment">Count: {{ count }}</button>
    <input v-model="message" placeholder="메시지 입력">
    <p>{{ message }}</p>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from 'vue'

// 반응형 데이터
const title = ref('Vue 3 시작하기')
const count = ref(0)
const message = ref('')

// 반응형 객체
const user = reactive({
  name: '홍길동',
  age: 30
})

// 계산된 속성
const doubleCount = computed(() => count.value * 2)

// 메서드
const increment = () => {
  count.value++
}

// 라이프사이클 훅
onMounted(() => {
  console.log('컴포넌트가 마운트되었습니다.')
})
</script>

<style scoped>
.hello {
  padding: 20px;
}

button {
  margin: 10px;
  padding: 10px 20px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
```

## 4. 필수 디렉티브

```vue
<template>
  <!-- 조건부 렌더링 -->
  <div v-if="isVisible">보이는 내용</div>
  <div v-else>숨겨진 내용</div>
  
  <!-- 리스트 렌더링 -->
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
  
  <!-- 이벤트 처리 -->
  <button @click="handleClick">클릭</button>
  <input @keyup.enter="handleEnter">
  
  <!-- 양방향 데이터 바인딩 -->
  <input v-model="inputValue">
  
  <!-- 속성 바인딩 -->
  <img :src="imageUrl" :alt="imageAlt">
  <div :class="{ active: isActive }">동적 클래스</div>
</template>

<script setup>
import { ref } from 'vue'

const isVisible = ref(true)
const items = ref([
  { id: 1, name: '아이템 1' },
  { id: 2, name: '아이템 2' },
  { id: 3, name: '아이템 3' }
])
const inputValue = ref('')
const imageUrl = ref('/path/to/image.jpg')
const imageAlt = ref('이미지 설명')
const isActive = ref(false)

const handleClick = () => {
  console.log('버튼이 클릭되었습니다.')
}

const handleEnter = () => {
  console.log('엔터키가 눌렸습니다.')
}
</script>
```

## 5. 컴포넌트 간 통신

### 부모 → 자식 (Props)
```vue
<!-- ParentComponent.vue -->
<template>
  <ChildComponent 
    :title="parentTitle" 
    :count="parentCount" 
    @update-count="handleUpdateCount"
  />
</template>

<script setup>
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const parentTitle = ref('부모에서 전달된 제목')
const parentCount = ref(0)

const handleUpdateCount = (newCount) => {
  parentCount.value = newCount
}
</script>
```

```vue
<!-- ChildComponent.vue -->
<template>
  <div>
    <h2>{{ title }}</h2>
    <p>Count: {{ count }}</p>
    <button @click="updateCount">증가</button>
  </div>
</template>

<script setup>
// Props 정의
const props = defineProps({
  title: {
    type: String,
    required: true
  },
  count: {
    type: Number,
    default: 0
  }
})

// Emit 정의
const emit = defineEmits(['update-count'])

const updateCount = () => {
  emit('update-count', props.count + 1)
}
</script>
```

## 6. 라우터 설정 (Vue Router)

```javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import AboutView from '../views/AboutView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/about',
      name: 'about',
      component: AboutView
    },
    {
      path: '/user/:id',
      name: 'user',
      component: () => import('../views/UserView.vue')
    }
  ]
})

export default router
```

### 라우터 사용
```vue
<template>
  <nav>
    <router-link to="/">홈</router-link>
    <router-link to="/about">소개</router-link>
    <router-link :to="{ name: 'user', params: { id: 123 }}">사용자</router-link>
  </nav>
  
  <!-- 라우터 뷰 -->
  <router-view />
</template>

<script setup>
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

// 프로그래밍 방식 네비게이션
const goToAbout = () => {
  router.push('/about')
}

// 현재 라우트 정보 접근
console.log(route.params.id) // URL 파라미터
console.log(route.query.page) // 쿼리 파라미터
</script>
```

## 7. 상태 관리 (Pinia)

```javascript
// stores/counter.js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  // 상태
  const count = ref(0)
  
  // 게터 (computed)
  const doubleCount = computed(() => count.value * 2)
  
  // 액션 (함수)
  function increment() {
    count.value++
  }
  
  function decrement() {
    count.value--
  }
  
  return { count, doubleCount, increment, decrement }
})
```

### 스토어 사용
```vue
<template>
  <div>
    <p>Count: {{ store.count }}</p>
    <p>Double Count: {{ store.doubleCount }}</p>
    <button @click="store.increment">+</button>
    <button @click="store.decrement">-</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()
</script>
```

## 8. API 호출 예제

```vue
<template>
  <div>
    <div v-if="loading">로딩 중...</div>
    <div v-else-if="error">에러: {{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="user in users" :key="user.id">
          {{ user.name }} - {{ user.email }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const users = ref([])
const loading = ref(false)
const error = ref(null)

const fetchUsers = async () => {
  try {
    loading.value = true
    error.value = null
    
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    if (!response.ok) {
      throw new Error('네트워크 응답이 올바르지 않습니다.')
    }
    
    users.value = await response.json()
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchUsers()
})
</script>
```

## 9. 실무 팁

### 폴더 구조
```
src/
├── components/     # 재사용 가능한 컴포넌트
├── views/         # 페이지 컴포넌트
├── stores/        # Pinia 스토어
├── router/        # 라우터 설정
├── composables/   # 재사용 가능한 로직
├── utils/         # 유틸리티 함수
└── assets/        # 정적 자원
```

### Composables 활용
```javascript
// composables/useCounter.js
import { ref } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  
  const increment = () => count.value++
  const decrement = () => count.value--
  const reset = () => count.value = initialValue
  
  return {
    count,
    increment,
    decrement,
    reset
  }
}
```

### 환경 변수 설정
```bash
# .env.development
VITE_API_URL=http://localhost:3000/api

# .env.production  
VITE_API_URL=https://api.myapp.com
```

```javascript
// 환경 변수 사용
const apiUrl = import.meta.env.VITE_API_URL
```
<br />

# 🔗 References
- [Vue 3 공식 문서](https://vuejs.org/)
- [Vue Router 공식 문서](https://router.vuejs.org/)
- [Pinia 공식 문서](https://pinia.vuejs.org/)
- [Vue 3 Composition API](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Vite 공식 문서](https://vitejs.dev/)
