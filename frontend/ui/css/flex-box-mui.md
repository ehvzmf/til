> 📅 Date: 2025-11-03

# 📌 Focus
- CSS Flexbox
- MUI Responsive Design
- React Layout Patterns
<br />

# 📝 Learnings

## Flexbox `flex` 속성 완벽 이해

### `flex` 속성 구조
**`flex` 속성은 3가지 값을 축약:**
- `flex-grow` (늘어날 수 있는 비율)
- `flex-shrink` (줄어들 수 있는 비율)  
- `flex-basis` (초기 크기)

### MUI 반응형 Flexbox 분석

#### **xs (모바일)**: `flex: '1 1 0'` (예시)
- `flex-grow: 1` → 남은 공간이 있으면 **늘어남**
- `flex-shrink: 1` → 공간이 부족하면 **줄어듦**
- `flex-basis: 0` → 초기 크기는 **0에서 시작** (컨텐츠 크기 무시하고 flex-grow로만 크기 결정)

**결과:** Stack이 부모 Flex 컨테이너의 남은 공간을 **모두 차지**합니다. (아이콘 24px 제외한 나머지 전부)

#### **lg (데스크탑)**: `flex: '0 1 50%'` (예시)
- `flex-grow: 0` → 늘어나지 **않음**
- `flex-shrink: 1` → 공간이 부족하면 **줄어듦**
- `flex-basis: 50%` → 초기 크기는 부모의 **50%**

**결과:** Stack이 부모 Flex 컨테이너의 **50% 크기로 고정**되려 하지만, 컨텐츠가 50%보다 크면 `flex-grow: 0`이라 **늘어나지 못하고 오버플로우** 발생 가능.

### 실제 동작 예시

```jsx
<Flex> {/* 부모 (width 설정됨) */}
  <Box (아이콘)> {/* 24px 고정 */}
  <Stack> {/* 이 부분에 flex 적용 */}
    <Typography (label)>
    <Typography (value)>
  </Stack>
</Flex>
```

- **xs**: Stack이 (부모 width - 아이콘 24px - gap 10px - padding)의 **100%** 차지
- **lg**: Stack이 부모 width의 **50%**만 차지하려고 함 → **긴 텍스트는 오버플로우**

### 문제점 및 해결방안

**문제:**
`lg`에서 `flex-basis: 50%` + `flex-grow: 0` 때문에:
- 긴 주소 텍스트가 50%를 넘으면 늘어나지 못함
- `wordBreak: 'keep-all'`로 띄어쓰기까지 안 끊으면 → **카드가 의도치 않게 넓어지거나 텍스트가 삐져나감**

**해결 방법:**

```jsx
// ❌ 문제 코드
<Stack sx={{ flex: { lg: '0 1 50%' } }}>
  <Typography sx={{ wordBreak: 'keep-all' }}>
    서울특별시 강남구 테헤란로 427, 위워크타워 10층
  </Typography>
</Stack>

// ✅ 해결 1: minWidth: 0 추가 (Flexbox 오버플로우 해결)
<Stack sx={{ 
  flex: { lg: '0 1 50%' },
  minWidth: 0  // 중요!
}}>
  <Typography sx={{ 
    wordBreak: 'keep-all',
    overflow: 'hidden',
    textOverflow: 'ellipsis'
  }}>
    긴 텍스트...
  </Typography>
</Stack>

// ✅ 해결 2: flex-grow 허용
<Stack sx={{ 
  flex: { lg: '1 1 50%' }  // grow를 1로 변경
}}>

// ✅ 해결 3: 최소/최대 너비 설정
<Stack sx={{ 
  flex: { lg: '0 1 50%' },
  minWidth: { lg: '300px' },
  maxWidth: { lg: '600px' }
}}>
```

---

## Flexbox 핵심 개념

### 컨테이너 속성 (부모)
- `display: flex` - Flex 컨테이너 활성화
- `flex-direction` - 주축 방향 (row, column)
- `justify-content` - 주축 정렬
- `align-items` - 교차축 정렬
- `flex-wrap` - 줄바꿈 처리
- `gap` - 아이템 간 간격

### 아이템 속성 (자식)
- `flex-grow` - 늘어나는 비율
- `flex-shrink` - 줄어드는 비율
- `flex-basis` - 초기 크기
- `flex` - 위 3개의 축약형
- `align-self` - 개별 아이템 정렬

### `flex` 값별 동작 패턴

| `flex` 값 | `flex-grow` | `flex-shrink` | `flex-basis` | 동작 |
|-----------|-------------|---------------|--------------|------|
| `1 1 0` | 1 (늘어남) | 1 (줄어듦) | 0 (컨텐츠 무시) | 남은 공간을 균등 분배 |
| `0 1 50%` | 0 (안 늘어남) | 1 (줄어듦) | 50% | 50%에서 시작, 공간 부족 시 축소 |
| `1 0 auto` | 1 (늘어남) | 0 (안 줄어듦) | auto (컨텐츠 기준) | 컨텐츠만큼 차지 후 늘어남 |
| `0 0 100px` | 0 (안 늘어남) | 0 (안 줄어듦) | 100px | 고정 크기 |

### 실험용 HTML

```html
<!DOCTYPE html>
<html>
<head>
<style>
.container {
  display: flex;
  width: 500px;
  height: 100px;
  border: 2px solid black;
  gap: 10px;
  padding: 10px;
}

.box {
  border: 2px solid red;
  padding: 10px;
}

/* 각각 테스트해보세요 */
.case1 { flex: 1 1 0; }
.case2 { flex: 0 1 50%; }
.case3 { flex: 1 0 auto; }
.case4 { flex: 0 0 100px; }
</style>
</head>
<body>
  <div class="container">
    <div class="box case1">Box 1</div>
    <div class="box case1">Box 2 with much longer content</div>
  </div>
</body>
</html>
```

---

## MUI 반응형 시스템

### Breakpoint 기본값

```javascript
const breakpoints = {
  xs: 0,      // 0px 이상 (모바일)
  sm: 600,    // 600px 이상 (태블릿)
  md: 900,    // 900px 이상 (작은 데스크탑)
  lg: 1200,   // 1200px 이상 (데스크탑)
  xl: 1536,   // 1536px 이상 (큰 화면)
};
```

### 반응형 스타일 작성 방법

```jsx
// 방법 1: Responsive Object
<Box sx={{
  flex: { 
    xs: '1 1 0',      // 0px ~ 1199px
    lg: '0 1 50%'     // 1200px 이상
  },
  padding: { xs: 2, md: 3, lg: 4 },
  fontSize: { xs: '14px', lg: '16px' }
}} />

// 방법 2: useMediaQuery Hook
import { useMediaQuery, useTheme } from '@mui/material';

function MyComponent() {
  const theme = useTheme();
  const isLargeScreen = useMediaQuery(theme.breakpoints.up('lg'));
  
  return (
    <Box sx={{
      flex: isLargeScreen ? '0 1 50%' : '1 1 0'
    }} />
  );
}

// 방법 3: sx prop에서 theme 활용
<Box sx={(theme) => ({
  flex: '1 1 0',
  [theme.breakpoints.up('lg')]: {
    flex: '0 1 50%',
  }
})} />
```

---

## 실전 레이아웃 패턴

### 패턴 1: 아이콘 + 텍스트 레이아웃

```jsx
<Box sx={{ display: 'flex', gap: 2, alignItems: 'center' }}>
  <Box sx={{ flex: '0 0 40px' }}>🎨</Box>
  <Box sx={{ flex: '1 1 0' }}>
    <Typography>제목</Typography>
    <Typography>설명</Typography>
  </Box>
</Box>
```

### 패턴 2: 네비게이션 바 (로고 + 메뉴 + 버튼)

```jsx
<Box sx={{ display: 'flex', gap: 2 }}>
  <Box sx={{ flex: '0 0 auto' }}>LOGO</Box>
  <Box sx={{ flex: '1 1 0' }}>Menu Items</Box>
  <Button sx={{ flex: '0 0 auto' }}>Login</Button>
</Box>
```

### 패턴 3: 균등 분배 카드

```jsx
// 2개 카드를 50:50으로
<Box sx={{ display: 'flex', gap: 2 }}>
  <Card sx={{ flex: '1 1 0' }}>Card 1</Card>
  <Card sx={{ flex: '1 1 0' }}>Card 2</Card>
</Box>

// 3개 카드를 33.33%씩
<Box sx={{ display: 'flex', gap: 2 }}>
  <Card sx={{ flex: '1 1 0' }}>Card 1</Card>
  <Card sx={{ flex: '1 1 0' }}>Card 2</Card>
  <Card sx={{ flex: '1 1 0' }}>Card 3</Card>
</Box>
```

### 패턴 4: 반응형 레이아웃

```jsx
// 모바일 세로, 데스크탑 가로
<Box sx={{ 
  display: 'flex', 
  flexDirection: { xs: 'column', md: 'row' },
  gap: 2 
}}>
  <Card sx={{ flex: { md: '1 1 0' } }}>Card 1</Card>
  <Card sx={{ flex: { md: '1 1 0' } }}>Card 2</Card>
</Box>

// 반응형 대시보드
<Box sx={{ 
  display: 'flex', 
  flexDirection: { xs: 'column', lg: 'row' },
  gap: 2 
}}>
  <Card sx={{ 
    flex: { xs: '1 1 auto', lg: '0 1 30%' },
    minWidth: { lg: 0 }
  }}>
    사이드바
  </Card>
  <Card sx={{ 
    flex: { xs: '1 1 auto', lg: '1 1 0' },
    minWidth: { lg: 0 }
  }}>
    메인 컨텐츠
  </Card>
</Box>
```

<br />

# 🔗 References

## 공식 문서
- [CSS Tricks - A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [MDN Web Docs - Flexbox](https://developer.mozilla.org/ko/docs/Learn/CSS/CSS_layout/Flexbox)
- [MUI Breakpoints](https://mui.com/material-ui/customization/breakpoints/)
- [MUI sx prop](https://mui.com/system/getting-started/the-sx-prop/)
