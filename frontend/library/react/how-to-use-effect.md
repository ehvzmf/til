useEffect: 외부 세계와의 인터페이스

**네, 맞습니다. 이건 명백히 외부 세계와 맞닿는 작업입니다.**

## 외부 세계의 정의

useEffect가 필요한 "외부 세계":
1. **브라우저 API**: DOM, localStorage, sessionStorage, cookies
2. **네트워크**: fetch, WebSocket
3. **타이머**: setTimeout, setInterval
4. **구독**: event listeners, observers
5. **외부 라이브러리**: jQuery, D3, 서드파티 SDK

## 당신의 코드 분석

```typescript
// ✅ 외부 세계: localStorage 읽기
useEffect(() => {
  const savedDraft = localStorage.getItem(storageKey);  // 브라우저 API
  // ...
}, [storageKey]);

// ✅ 외부 세계: localStorage 쓰기
useEffect(() => {
  localStorage.setItem(storageKey, JSON.stringify(draft));  // 브라우저 API
  // 또는
  localStorage.removeItem(storageKey);  // 브라우저 API
}, [content, selectedPollmimcon, storageKey]);
```

**이건 100% 올바른 useEffect 사용입니다.**

## 왜냐하면:

1. **Side Effect**: localStorage 조작은 React 렌더링 흐름 밖의 작업
2. **동기화**: React state(content)와 외부 시스템(localStorage)을 동기화
3. **의존성 추적**: storageKey, content 변경 시 자동으로 localStorage 업데이트

## 잘못된 useEffect 예시 (비교용)

```typescript
// ❌ 나쁜 예: 단순 계산은 useEffect 불필요
useEffect(() => {
  setTotal(price * quantity);  // 그냥 렌더링 중에 계산하면 됨
}, [price, quantity]);

// ✅ 좋은 예: 렌더링 중 계산
const total = price * quantity;

// ❌ 나쁜 예: state 변환은 useEffect 불필요
useEffect(() => {
  setUpperCase(text.toUpperCase());
}, [text]);

// ✅ 좋은 예: 렌더링 중 변환
const upperCase = text.toUpperCase();
```

**결론: 당신의 코드는 정확합니다. localStorage는 명백한 외부 세계이고, useEffect로 동기화하는 게 올바른 패턴입니다.** 👍

## 외부 세계의 명확한 정의

**외부 세계 = React가 관리하지 않는 모든 것**

React는 컴포넌트의 state, props, JSX 렌더링만 관리합니다. 그 외의 모든 것은 "외부 세계"입니다.

---

## 비유로 이해하기

React 컴포넌트를 **집** 안이라고 생각하세요.

### 🏠 집 안 (React 세계)
- **state**: 집 안 가구 배치
- **props**: 가족이 주는 물건
- **JSX**: 집 안 인테리어

이것들은 React가 자동으로 관리합니다. 값이 바뀌면 React가 알아서 화면을 다시 그립니다.

### 🌍 집 밖 (외부 세계)
- **localStorage**: 집 밖 창고
- **DOM API**: 집 밖 도로, 건물
- **서버 API**: 다른 동네
- **타이머**: 집 밖 시계탑
- **이벤트 리스너**: 집 밖 우체통

이것들은 **React가 모릅니다**. 당신이 직접 나가서 처리해야 합니다. 그게 바로 `useEffect`의 역할입니다.

---

## 구체적인 예시들

### ✅ 외부 세계 (useEffect 필요)

```typescript
// 1. localStorage (브라우저 저장소)
useEffect(() => {
  localStorage.setItem('theme', 'dark');  // 집 밖 창고에 물건 보관
}, [theme]);

// 2. DOM 조작
useEffect(() => {
  document.title = `${count} notifications`;  // 브라우저 탭 제목 변경
}, [count]);

// 3. 이벤트 리스너
useEffect(() => {
  const handleResize = () => setWidth(window.innerWidth);
  window.addEventListener('resize', handleResize);  // 외부 이벤트 구독
  return () => window.removeEventListener('resize', handleResize);  // 구독 해제
}, []);

// 4. 타이머
useEffect(() => {
  const timer = setTimeout(() => setShow(false), 3000);  // 브라우저 타이머
  return () => clearTimeout(timer);
}, []);

// 5. 서버 통신 (fetch)
useEffect(() => {
  fetch('/api/user')  // 서버에 요청
    .then(res => res.json())
    .then(setUser);
}, []);

// 6. 외부 라이브러리
useEffect(() => {
  const chart = new Chart(canvasRef.current);  // D3, Chart.js 등
  return () => chart.destroy();
}, [data]);
```

### ❌ 내부 세계 (useEffect 불필요)

```typescript
// 1. 단순 계산 - 렌더링 중에 하면 됨
// ❌ 나쁜 코드
const [total, setTotal] = useState(0);
useEffect(() => {
  setTotal(price * quantity);
}, [price, quantity]);

// ✅ 좋은 코드
const total = price * quantity;  // 그냥 계산


// 2. 데이터 변환 - 렌더링 중에 하면 됨
// ❌ 나쁜 코드
const [filtered, setFiltered] = useState([]);
useEffect(() => {
  setFiltered(items.filter(item => item.active));
}, [items]);

// ✅ 좋은 코드
const filtered = items.filter(item => item.active);  // 그냥 필터링


// 3. state 업데이트 체인 - 이벤트 핸들러에서 하면 됨
// ❌ 나쁜 코드
useEffect(() => {
  if (isSubmitted) {
    setLoading(false);
    setSuccess(true);
  }
}, [isSubmitted]);

// ✅ 좋은 코드
const handleSubmit = () => {
  setIsSubmitted(true);
  setLoading(false);
  setSuccess(true);
};
```

---

## 핵심 판단 기준

### "이건 useEffect가 필요한가?" 체크리스트

#### ✅ useEffect 필요 (외부 세계)
- [ ] React 밖의 시스템과 통신하나? (localStorage, DOM, 서버)
- [ ] 구독/해제가 필요한가? (이벤트, 타이머, WebSocket)
- [ ] 외부 라이브러리를 초기화/정리하나?

#### ❌ useEffect 불필요 (내부 세계)
- [ ] 단순히 값을 계산하는 건가?
- [ ] props나 state에서 파생된 값인가?
- [ ] 사용자 이벤트에 대한 응답인가? (클릭, 제출 등)

---

## 당신의 코드 다시 분석

```typescript
// ✅ 외부 세계: localStorage는 React 밖 브라우저 API
useEffect(() => {
  const savedDraft = localStorage.getItem(storageKey);  // 집 밖 창고에서 가져옴
  if (savedDraft) {
    setContent(parsed.content);  // 집 안(state)에 저장
  }
}, [storageKey]);

// ✅ 외부 세계: React state를 localStorage에 동기화
useEffect(() => {
  localStorage.setItem(storageKey, JSON.stringify(draft));  // 집 밖 창고에 보관
}, [content, selectedPollmimcon, storageKey]);
```

**왜 외부 세계인가?**
- localStorage는 브라우저 API (React가 모름)
- 페이지 새로고침해도 남아있음 (React 생명주기 밖)
- 다른 탭에서도 접근 가능 (React 컴포넌트 범위 밖)

**만약 useEffect 없이 하면?**
```typescript
// ❌ 이렇게 하면 안 됨
const [content, setContent] = useState('');
localStorage.setItem('draft', content);  // 렌더링마다 실행됨! 무한 루프 위험

// 렌더링 → localStorage 쓰기 → 리렌더링 → localStorage 쓰기 → ...
```

---

## 최종 정리

| | React 세계 (집 안) | 외부 세계 (집 밖) |
|---|---|---|
| **관리자** | React가 자동 관리 | 개발자가 수동 관리 |
| **예시** | state, props, JSX | localStorage, DOM, 서버, 타이머 |
| **코드 위치** | 렌더링 로직 | useEffect |
| **변경 감지** | React가 자동 | useEffect 의존성 배열 |
| **정리** | 자동 | return cleanup 함수 |

**외부 세계 = React 렌더링 흐름 밖에서 일어나는 모든 side effect**

## 각각을 구체적으로 정의해드릴게요

---

## 1. DOM API란?

**DOM = Document Object Model (문서 객체 모델)**

HTML 문서를 JavaScript로 조작할 수 있게 해주는 브라우저 API입니다.

### 구체적인 예시:

```typescript
// React 방식 (React 세계 - 내부)
return <h1>Hello</h1>  // JSX로 화면 그림

// DOM API 방식 (외부 세계)
document.querySelector('h1')  // HTML 요소에 직접 접근
document.title = 'New Title'  // 브라우저 탭 제목 변경
document.body.style.backgroundColor = 'red'  // body 색상 변경
window.scrollTo(0, 100)  // 스크롤 위치 변경
element.focus()  // 특정 input에 포커스
element.getBoundingClientRect()  // 요소의 위치/크기 정보
```

### 왜 외부 세계인가?

```typescript
// ❌ 렌더링 중에 DOM 조작하면 안 됨
function MyComponent() {
  document.title = 'Hello';  // 렌더링마다 실행됨! 버그 위험
  return <div>Content</div>;
}

// ✅ useEffect로 DOM 조작
function MyComponent() {
  useEffect(() => {
    document.title = 'Hello';  // 렌더링 후에 한 번만 실행
  }, []);
  return <div>Content</div>;
}
```

**이유**: React는 JSX로 화면을 그리지만, `document.title`이나 `window.scrollTo` 같은 건 React가 관리하지 않습니다. 직접 브라우저에 명령을 내리는 것입니다.

---

## 2. 외부 이벤트란?

**브라우저나 사용자 환경에서 발생하는 이벤트**

React 컴포넌트 밖에서 발생하는 모든 이벤트입니다.

### 구체적인 예시:

```typescript
// React 이벤트 (내부 세계 - useEffect 불필요)
<button onClick={() => setCount(count + 1)}>  // JSX 안에서 처리
  Click me
</button>

// 외부 이벤트 (외부 세계 - useEffect 필요)
useEffect(() => {
  // 1. 윈도우 리사이즈
  const handleResize = () => {
    setWidth(window.innerWidth);
  };
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);

useEffect(() => {
  // 2. 키보드 이벤트 (전역)
  const handleKeyPress = (e) => {
    if (e.key === 'Escape') setModalOpen(false);
  };
  document.addEventListener('keydown', handleKeyPress);
  return () => document.removeEventListener('keydown', handleKeyPress);
}, []);

useEffect(() => {
  // 3. 스크롤 이벤트
  const handleScroll = () => {
    setScrollY(window.scrollY);
  };
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);

useEffect(() => {
  // 4. 브라우저 포커스/블러
  const handleVisibility = () => {
    if (document.hidden) {
      pauseVideo();
    }
  };
  document.addEventListener('visibilitychange', handleVisibility);
  return () => document.removeEventListener('visibilitychange', handleVisibility);
}, []);
```

### 왜 외부 세계인가?

```typescript
// React 버튼 클릭 (내부)
<button onClick={handleClick}>  // React가 관리하는 이벤트

// 윈도우 리사이즈 (외부)
window.addEventListener('resize', ...)  // 브라우저가 발생시키는 이벤트
```

**차이점**:
- `onClick`은 **React 요소**에 붙어있음 → React가 관리
- `window.addEventListener`는 **브라우저**에 붙어있음 → React가 모름

---

## 3. 타이머가 왜 외부 세계인가?

**타이머 = 브라우저의 시계 시스템**

`setTimeout`, `setInterval`은 JavaScript의 함수가 아니라 **브라우저가 제공하는 API**입니다.

### 구체적인 예시:

```typescript
// ❌ 렌더링 중에 타이머 설정하면 안 됨
function Toast() {
  const [show, setShow] = useState(true);
  
  setTimeout(() => setShow(false), 3000);  // 렌더링마다 타이머 생성! 메모리 누수
  
  return show ? <div>Toast</div> : null;
}

// ✅ useEffect로 타이머 관리
function Toast() {
  const [show, setShow] = useState(true);
  
  useEffect(() => {
    const timer = setTimeout(() => setShow(false), 3000);  // 한 번만 생성
    return () => clearTimeout(timer);  // 컴포넌트 사라질 때 정리
  }, []);
  
  return show ? <div>Toast</div> : null;
}
```

### 왜 외부 세계인가?

타이머는 **브라우저의 백그라운드**에서 돌아갑니다.

```typescript
setTimeout(() => {
  console.log('3초 후');
}, 3000);

// 이 코드는 브라우저에게 다음과 같이 말하는 것:
// "3초 뒤에 이 함수 실행해줘"
```

**문제 상황**:
```typescript
function Counter() {
  const [count, setCount] = useState(0);
  
  // ❌ 이렇게 하면 안 됨
  setInterval(() => setCount(c => c + 1), 1000);  // 렌더링마다 새 타이머 생성!
  
  return <div>{count}</div>;
}

// 렌더링 → 타이머 1개 생성
// 1초 후 count 변경 → 리렌더링 → 타이머 2개 생성 (기존 것 + 새 것)
// 1초 후 count 변경 → 리렌더링 → 타이머 3개 생성
// ... 타이머가 계속 쌓임! (메모리 누수)
```

```typescript
// ✅ 올바른 방법
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => setCount(c => c + 1), 1000);
    return () => clearInterval(interval);  // 정리
  }, []);
  
  return <div>{count}</div>;
}

// 마운트 → 타이머 1개 생성
// 언마운트 → 타이머 정리
```

---

## 4. 외부 라이브러리가 왜 외부 세계인가?

**외부 라이브러리 = React가 모르는 코드**

Chart.js, D3, jQuery, Leaflet 같은 라이브러리는 React와 별개로 동작합니다.

### 구체적인 예시:

```typescript
// Chart.js 예시
function ChartComponent() {
  const canvasRef = useRef(null);
  
  useEffect(() => {
    const chart = new Chart(canvasRef.current, {  // Chart.js 인스턴스 생성
      type: 'bar',
      data: { labels: ['A', 'B'], datasets: [{ data: [10, 20] }] }
    });
    
    return () => chart.destroy();  // 인스턴스 정리
  }, []);
  
  return <canvas ref={canvasRef} />;
}

// Leaflet 지도 예시
function MapComponent() {
  const mapRef = useRef(null);
  
  useEffect(() => {
    const map = L.map(mapRef.current).setView([37.5, 127], 13);  // 지도 생성
    L.tileLayer('https://...').addTo(map);
    
    return () => map.remove();  // 지도 정리
  }, []);
  
  return <div ref={mapRef} style={{ height: '400px' }} />;
}
```

### 왜 외부 세계인가?

```typescript
// React 방식 (React가 관리)
const [count, setCount] = useState(0);
// React가 count 변경을 감지하고 화면 업데이트

// Chart.js 방식 (React가 모름)
const chart = new Chart(canvas, config);
chart.data.datasets[0].data.push(100);  // 차트 업데이트
chart.update();
// React는 이 변경을 모름!
```

**문제 상황**:
```typescript
// ❌ 렌더링마다 차트 생성
function Chart() {
  const ref = useRef(null);
  const chart = new Chart(ref.current, config);  // 렌더링마다 차트 생성! 메모리 누수
  return <canvas ref={ref} />;
}

// ✅ 올바른 방법
function Chart() {
  const ref = useRef(null);
  
  useEffect(() => {
    const chart = new Chart(ref.current, config);  // 한 번만 생성
    return () => chart.destroy();  // 정리
  }, []);
  
  return <canvas ref={ref} />;
}
```

---

## 핵심 정리: 왜 이것들이 외부 세계인가?

| 항목 | React가 관리하는가? | 정리가 필요한가? | useEffect 필요 |
|---|---|---|---|
| state | ✅ 예 | ❌ 아니오 | ❌ |
| JSX | ✅ 예 | ❌ 아니오 | ❌ |
| **DOM API** | ❌ 아니오 | 때때로 | ✅ |
| **외부 이벤트** | ❌ 아니오 | ✅ 예 (removeListener) | ✅ |
| **타이머** | ❌ 아니오 | ✅ 예 (clear) | ✅ |
| **외부 라이브러리** | ❌ 아니오 | ✅ 예 (destroy) | ✅ |
| **localStorage** | ❌ 아니오 | ❌ 아니오 | ✅ |

### 판단 기준:

```
1. React가 이 값의 변경을 자동으로 감지하는가?
   → 아니오 → 외부 세계

2. 컴포넌트가 사라질 때 정리해야 하는가?
   → 예 → 외부 세계

3. 브라우저 API를 직접 호출하는가?
   → 예 → 외부 세계
```




