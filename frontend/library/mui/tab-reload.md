# TIL: MUI Tabs 활성 탭 재클릭 시 이벤트가 발생하지 않는 문제

## 문제 상황

React에서 MUI(Material-UI)의 Tabs 컴포넌트를 사용할 때, 이미 활성화된 탭을 다시 클릭해도 `onChange` 이벤트가 발생하지 않는 문제를 발견했습니다.

원하는 동작:
- 사용자가 현재 활성화된 탭을 다시 클릭할 때 페이지 새로고침
- 스크롤이 맨 위로 올라가도록 처리

하지만 MUI Tabs는 이미 선택된 탭을 다시 클릭할 때 이벤트를 발생시키지 않아 이 기능을 구현하기 어려웠습니다.

## 코드 예시

아래는 이 문제를 보여주는 간소화된 코드입니다:

```tsx
import { Box, Tabs, Tab, useTheme } from '@mui/material';
import { useLocation, useNavigate } from 'react-router-dom';
import { SyntheticEvent } from 'react';

const tabs = [
  { label: '홈', path: '/' },
  { label: '인기투표', path: '/vote' },
  { label: '게시판', path: '/board' },
];

export const NavigationTab = () => {
  const theme = useTheme();
  const location = useLocation();
  const navigate = useNavigate();

  const getCurrentTab = () => {
    const currentPath = location.pathname;
    
    for (let i = 0; i < tabs.length; i++) {
      if (tabs[i].path === '/') {
        if (currentPath === '/') return i;
      } else {
        if (currentPath.startsWith(tabs[i].path)) return i;
      }
    }
    
    return false;
  };

  const currentTab = getCurrentTab();
  
  const handleChange = (event: SyntheticEvent, newValue: number) => {
    const targetPath = tabs[newValue].path;
    const currentPath = location.pathname;
    
    // trim() 추가하여 확실한 매칭 확인
    const cleanTargetPath = targetPath.trim();
    const cleanCurrentPath = currentPath.trim();
    
    console.log(`"${cleanTargetPath}" === "${cleanCurrentPath}"?`, cleanTargetPath === cleanCurrentPath);
    
    if (cleanTargetPath === cleanCurrentPath) {
      window.location.reload();
    } else {
      navigate(cleanTargetPath);
    }
  };

  return (
    <Box sx={{ mb: '6px' }}>
      <Tabs
        value={currentTab}
        onChange={handleChange}
        variant='standard'
        centered
        textColor="inherit"
        // 스타일 관련 속성들...
      >
        {tabs.map((tab) => (
          <Tab key={tab.path} label={tab.label} />
        ))}
      </Tabs>     
    </Box>
  );
}
```

## 핵심 문제점

MUI Tabs 컴포넌트는 **이미 활성화된 탭(value로 지정된 탭)을 다시 클릭하면 onChange 이벤트를 발생시키지 않도록** 설계되어 있습니다. 이는 MUI의 의도적인 디자인 결정으로, 불필요한 렌더링을 방지하기 위함입니다.

## 해결 방법

이 문제를 해결하기 위한 몇 가지 접근법:

### 1. 각 Tab에 onClick 이벤트 추가하기

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

이 방법은 Tab 컴포넌트에 직접 onClick 이벤트 핸들러를 추가합니다. 현재 탭과 클릭된 탭이 동일하면 페이지를 새로고침합니다.

### 2. 커스텀 Tab 컴포넌트 만들기

```tsx
const CustomTab = (props) => {
  const { value, index, ...other } = props;
  const isSelected = value === index;
  
  const handleClick = () => {
    if (isSelected) {
      window.location.reload();
    }
  };
  
  return <Tab onClick={handleClick} {...other} />;
};

// 사용 예시
{tabs.map((tab, index) => (
  <CustomTab 
    key={tab.path} 
    label={tab.label}
    value={currentTab}
    index={index}
  />
))}
```

### 3. useEffect와 참조 변수 활용

```tsx
const NavigationTab = () => {
  // ... 기존 코드 ...
  
  const prevTabRef = useRef(currentTab);
  
  useEffect(() => {
    if (prevTabRef.current === currentTab) {
      // 같은 탭을 다시 클릭했을 때의 처리
      window.scrollTo(0, 0);
      // 필요하다면 여기서 추가 작업 수행
    }
    prevTabRef.current = currentTab;
  }, [currentTab]);
  
  // ... 나머지 코드 ...
};
```

## 결론

MUI Tabs 컴포넌트는 사용자 경험을 최적화하기 위해 이미 활성화된 탭을 다시 클릭해도 onChange 이벤트를 발생시키지 않습니다. 같은 탭을 클릭했을 때 특정 동작(새로고침, 스크롤 위치 초기화 등)을 원한다면, 위에서 제안한 방법 중 하나를 사용하여 이 제한을 우회할 수 있습니다.

이런 작은 UX 디테일에 신경 쓰는 것이 사용자 경험을 향상시키는 데 큰 도움이 됩니다.
