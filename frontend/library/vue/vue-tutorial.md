> 📅 Date: 2025-10-03

# 📌 Focus
- Vue.js 프레임워크
- Progressive JavaScript Framework
- 컴포넌트 기반 개발
- 반응형 데이터 바인딩

<br />

# 📝 Learnings

## Vue.js
- 사용자 인터페이스를 구축하기 위한 **Progressive JavaScript Framework**
- 점진적으로 채택 가능한 설계 -> 기존 프로젝트에 부분 도입/완전한 SPA 구축 가능

## 주요 특징

### 1. 반응형 데이터 바인딩 (Reactive Data Binding)
데이터가 변경되면 자동으로 DOM 업데이트

```vue
<template>
  <div>
    <p>{{ message }}</p>
    <button @click="updateMessage">메시지 변경</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello Vue!'
    }
  },
  methods: {
    updateMessage() {
      this.message = 'Vue.js is awesome!'
    }
  }
}
</script>
```

### 성

```vue
<!-- ParentComponent.vue -->
<template>
  <div>
    <ChildComponent :title="pageTitle" @update="handleUpdate" />
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue'

export default {
  components: {
    ChildComponent
  },
  data() {
    return {
      pageTitle: '부모 컴포넌트'
    }
  },
  methods: {
    handleUpdate(newValue) {
      console.log('자식에서 전달받은 값:', newValue)
    }
  }
}
</script>
```

### 3. 디렉티브 (Directives)
HTML 템플릿에서 Vue의 기능을 활용할 수 있는 특별한 속성들입니다.

```vue
<template>
  <div>
    <!-- 조건부 렌더링 -->
    <p v-if="isVisible">보이는 텍스트</p>
    <p v-else>숨겨진 텍스트</p>
    
    <!-- 리스트 렌더링 -->
    <ul>
      <li v-for="item in items" :key="item.id">
        {{ item.name }}
      </li>
    </ul>
    
    <!-- 양방향 데이터 바인딩 -->
    <input v-model="inputValue" placeholder="입력하세요">
    <p>입력값: {{ inputValue }}</p>
    
    <!-- 이벤트 처리 -->
    <button @click="handleClick">클릭</button>
  </div>
</template>
```

## Vue 3의 주요 변화점

### 1. Composition API
기존 Options API 외에 함수형 컴포넌트 작성 방식이 추가되었습니다.

```vue
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="increment">증가</button>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'

export default {
  setup() {
    // 반응형 상태
    const count = ref(0)
    
    // 계산된 속성
    const doubledCount = computed(() => count.value * 2)
    
    // 메서드
    const increment = () => {
      count.value++
    }
    
    // 라이프사이클 훅
    onMounted(() => {
      console.log('컴포넌트가 마운트되었습니다')
    })
    
    return {
      count,
      doubledCount,
      increment
    }
  }
}
</script>
```

### 2. Multiple Root Elements
Vue 3에서는 컴포넌트가 여러 개의 루트 엘리먼트를 가질 수 있습니다.

```vue
<template>
  <header>헤더</header>
  <main>메인 콘텐츠</main>
  <footer>푸터</footer>
</template>
```

### 3. Teleport
컴포넌트의 일부를 DOM의 다른 위치로 렌더링할 수 있습니다.

```vue
<template>
  <div>
    <button @click="open = true">모달 열기</button>
    
    <teleport to="body">
      <div v-if="open" class="modal">
        <p>모달 내용</p>
        <button @click="open = false">닫기</button>
      </div>
    </teleport>
  </div>
</template>
```

## Vue 생태계

### 1. Vue Router - 라우팅
SPA에서 페이지 간 네비게이션을 처리합니다.

```js
import { createRouter, createWebHistory } from 'vue-router'
import Home from './components/Home.vue'
import About from './components/About.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})
```

### 2. Pinia (Vuex 4 대체) - 상태 관리
애플리케이션의 전역 상태를 관리합니다.

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  getters: {
    doubleCount: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

### 3. Vue CLI / Vite
프로젝트 설정과 빌드 도구입니다.

```bash
# Vue CLI
npm install -g @vue/cli
vue create my-project

# Vite (권장)
npm create vue@latest my-project
```

## 실무에서의 Vue.js 활용

### 1. 기존 프로젝트에 점진적 도입
```html
<!-- 기존 HTML 페이지에 Vue 추가 -->
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">
  <h1>{{ title }}</h1>
  <button @click="changeTitle">제목 변경</button>
</div>

<script>
const { createApp } = Vue

createApp({
  data() {
    return {
      title: '기존 프로젝트에 Vue 추가'
    }
  },
  methods: {
    changeTitle() {
      this.title = '제목이 변경되었습니다!'
    }
  }
}).mount('#app')
</script>
```

### 2. 컴포넌트 재사용성
```vue
<!-- BaseButton.vue -->
<template>
  <button 
    :class="['btn', `btn--${variant}`, { 'btn--loading': loading }]"
    :disabled="disabled || loading"
    @click="$emit('click')"
  >
    <span v-if="loading">로딩 중...</span>
    <slot v-else></slot>
  </button>
</template>

<script>
export default {
  props: {
    variant: {
      type: String,
      default: 'primary'
    },
    loading: Boolean,
    disabled: Boolean
  },
  emits: ['click']
}
</script>
```

### 3. 폼 처리와 유효성 검사
```vue
<template>
  <form @submit.prevent="submitForm">
    <div>
      <label>이메일:</label>
      <input 
        v-model="form.email" 
        type="email" 
        :class="{ error: errors.email }"
      >
      <span v-if="errors.email" class="error-message">
        {{ errors.email }}
      </span>
    </div>
    
    <div>
      <label>비밀번호:</label>
      <input 
        v-model="form.password" 
        type="password"
        :class="{ error: errors.password }"
      >
      <span v-if="errors.password" class="error-message">
        {{ errors.password }}
      </span>
    </div>
    
    <button type="submit" :disabled="!isFormValid">
      로그인
    </button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      form: {
        email: '',
        password: ''
      },
      errors: {}
    }
  },
  computed: {
    isFormValid() {
      return this.form.email && 
             this.form.password && 
             Object.keys(this.errors).length === 0
    }
  },
  watch: {
    'form.email'() {
      this.validateEmail()
    },
    'form.password'() {
      this.validatePassword()
    }
  },
  methods: {
    validateEmail() {
      if (!this.form.email) {
        this.$set(this.errors, 'email', '이메일을 입력하세요')
      } else if (!this.isValidEmail(this.form.email)) {
        this.$set(this.errors, 'email', '올바른 이메일 형식이 아닙니다')
      } else {
        this.$delete(this.errors, 'email')
      }
    },
    validatePassword() {
      if (!this.form.password) {
        this.$set(this.errors, 'password', '비밀번호를 입력하세요')
      } else if (this.form.password.length < 6) {
        this.$set(this.errors, 'password', '비밀번호는 6자 이상이어야 합니다')
      } else {
        this.$delete(this.errors, 'password')
      }
    },
    isValidEmail(email) {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
    },
    submitForm() {
      console.log('폼 제출:', this.form)
    }
  }
}
</script>
```

## Vue vs React vs Angular 비교

| 특징 | Vue.js | React | Angular |
|------|--------|-------|---------|
| 학습 곡선 | 완만함 | 보통 | 가파름 |
| 템플릿 | HTML 기반 | JSX | HTML + TypeScript |
| 상태 관리 | Pinia/Vuex | Redux/Context | Services/NgRx |
| 크기 | 작음 (~34KB) | 보통 (~42KB) | 큼 (~130KB) |
| 성능 | 우수 | 우수 | 우수 |
| 생태계 | 중간 | 매우 큼 | 큼 |

## 실무 적용 팁

### 1. 성능 최적화
- `v-show` vs `v-if` 적절히 사용
- `key` 속성으로 리스트 렌더링 최적화
- 계산된 속성(computed) 활용으로 불필요한 연산 방지

### 2. 컴포넌트 설계
- 단일 책임 원칙 적용
- Props/Events를 통한 명확한 데이터 흐름
- Slot을 활용한 유연한 컴포넌트 구조

### 3. 코드 품질
- ESLint + Prettier 설정
- TypeScript 도입 고려
- 컴포넌트 테스트 작성 (Vue Test Utils)

<br />

# 🔗 References
- [Vue.js 공식 문서](https://vuejs.org/)
- [Vue.js 한국어 문서](https://v3.ko.vuejs.org/)
- [Vue Router](https://router.vuejs.org/)
- [Pinia](https://pinia.vuejs.org/)
- [Vue CLI](https://cli.vuejs.org/)
- [Vite](https://vitejs.dev/)
- [Vue Test Utils](https://test-utils.vuejs.org/)
