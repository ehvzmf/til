> 📝 Date: 2025-11-05

> 🐛 Issue Date: 2025-08-04

<br />

# 🐛 Issue
- MUI Tabs에서 활성 탭 재클릭 시 onChange 이벤트가 발생하지 않아 새로고침 기능 구현 불가

<br />

# 🔍 Context
- 환경: React 18, MUI 5, React Router DOM 6
- 발생 조건: 이미 활성화된 탭을 다시 클릭할 때 페이지 새로고침 또는 스크롤 초기화가 필요한 상황

<br />

# 💡 Solution
## 시도한 방법들
1. ❌ handleChange에서 현재 path와 비교 - 이벤트 자체가 발생하지 않음
2. ❌ useEffect로 현재 탭 변화 감지 - 같은 탭 클릭 시 값이 변하지 않음
3. ✅ 각 Tab에 onClick 이벤트 직접 추가

<br />

## 최종 해결 방법

### 원인
MUI Tabs는 이미 활성화된 탭 재클릭 시 onChange 이벤트를 발생시키지 않도록 설계됨 (불필요한 렌더링 방지)

### 해결책 1: Tab 컴포넌트에 onClick 추가
```tsx
{tabs.map((tab, index) => (
  <Tab 
    key={tab.path} 
    label={tab.label} 
    onClick={() => {
      if (currentTab === index) {
        window.location.reload();
      }
    }}
  />
))}
```

### 해결책 2: 커스텀 Tab 컴포넌트
```tsx
const CustomTab = ({ value, index, onClick, ...props }) => {
  const isSelected = value === index;
  
  const handleClick = (event) => {
    if (isSelected) {
      window.location.reload();
    }
    onClick?.(event);
  };
  
  return <Tab onClick={handleClick} {...props} />;
};
```

### 해결책 3: 스크롤만 초기화하는 경우
```tsx
{tabs.map((tab, index) => (
  <Tab 
    key={tab.path} 
    label={tab.label} 
    onClick={() => {
      if (currentTab === index) {
        window.scrollTo(0, 0);
        // 추가적으로 상태 초기화 등 수행
      }
    }}
  />
))}
```
