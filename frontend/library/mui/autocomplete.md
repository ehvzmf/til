> 📅 Date: 2025-09-18

# 📌 Focus
- Material-UI (MUI)
- AutoComplete Component
<br />

# 📝 Learnings
## `AutoComplete`
- 텍스트 입력 -> 추천 옵션 목록을 드롭다운 형태로 보여주는 입력 필드
- 검색창, 태그 선택, 폼 입력 등 활용 

### 주요 특징
- **향상된 텍스트 필드**: 일반 `TextField`에 자동 완성 기능 결합
- **다양한 데이터 소스**: 정적인 배열부터 비동기 API를 통해 가져온 동적인 데이터까지 사용 가능
- **커스터마이징**: 옵션 목록의 디자인, 필터링 방식, 선택 동작 등 자유로운 설정
- **다중 선택**: `multiple` 속성을 통해 여러 값 선택 가능 

### 기본 사용법 예제
```jsx
import * as React from 'react';
import TextField from '@mui/material/TextField';
import Autocomplete from '@mui/material/Autocomplete';

const top100Films = [
  { label: 'The Shawshank Redemption', year: 1994 },
  { label: 'The Godfather', year: 1972 },
  { label: 'The Godfather: Part II', year: 1974 },
];

export default function ComboBox() {
  return (
    <Autocomplete
      disablePortal
      id="combo-box-demo"
      options={top100Films}
      sx={{ width: 300 }}
      renderInput={(params) => <TextField {...params} label="Movie" />}
    />
  );
}
```
- **`options`**: 자동 완성 목록에 표시될 데이터 배열입니다. 각 객체는 기본적으로 `label` 키를 가집니다.
- **`renderInput`**: 입력 필드 부분을 렌더링하는 함수로, 보통 `TextField` 컴포넌트가 사용됩니다.

<br />

### 실제 활용 예시
```tsx
import { useState } from 'react';
import { Autocomplete, TextField, useTheme } from '@mui/material';

// 추천 검색어 목록 데이터
const suggestionOptions = ['Apple', 'Banana', 'Cherry', 'Durian', 'Elderberry'];

function AdvancedAutocomplete() {
  const theme = useTheme();
  const [selectedValue, setSelectedValue] = useState('');

  return (
    <Autocomplete
      // 1. 값 및 변경 핸들러
      value={selectedValue}
      onChange={(_, newValue) => setSelectedValue(newValue || '')}
      
      // 2. 옵션 및 동작 제어
      options={suggestionOptions}
      freeSolo
      autoSelect
      selectOnFocus
      handleHomeEndKeys
      
      // 3. 드롭다운 목록 스타일
      ListboxProps={{
        style: {
          maxHeight: 244, // 목록의 최대 높이를 244px로 제한
        }
      }}
      
      // 4. 입력 필드 렌더링 및 커스텀 스타일
      renderInput={(params) => (
        <TextField 
          {...params}
          placeholder="과일 이름 입력..."
          fullWidth
          sx={{
            '& .MuiOutlinedInput-root': {
              borderRadius: 0,
              height: '60px',
              // 기본 상태에서는 테두리 없음
              '& fieldset': {
                border: 'none',
              },
              // 마우스 호버 시에도 테두리 없음
              '&:hover fieldset': {
                border: 'none',
              },
              // 포커스되었을 때만 회색 테두리 표시
              '&.Mui-focused fieldset': {
                border: `1px solid ${theme.palette.grey[700]}`,
              },
            },
            // 입력 필드 내부 패딩 조절
            '& .MuiInputBase-input': {
              padding: '16px 14px',
            }
          }}
        />
      )}
      
      // 5. 드롭다운(Popper) 위치 및 동작 커스터마이징
      slotProps={{
        popper: {
          placement: 'bottom-start', // 항상 아래쪽-시작 지점에 표시
          modifiers: [
            {
              name: 'flip', // 화면 공간이 부족할 때 위아래로 뒤집히는 동작 비활성화
              enabled: false,
            },
            {
              name: 'preventOverflow', // 화면 밖으로 벗어나는 것을 방지하는 동작 비활성화
              enabled: false,
            },
          ],
        },
      }}
    />
  );
}

export default AdvancedAutocomplete;
```
### 속성(Props) 설명
#### 옵션 및 동작 제어
- `options`: 드롭다운에 표시될 추천 항목 배열.
- `freeSolo`: true 시, 목록에 없는 텍스트도 자유롭게 입력 가능.
- `autoSelect`: true 시, 키보드 탐색 시 하이라이트된 항목을 자동 선택 후보로 지정.
- `selectOnFocus`: 입력 필드 포커스 시 내부 텍스트 전체를 선택 상태로 만듦.
- `handleHomeEndKeys`: Home/End 키로 목록의 맨 처음/끝으로 빠른 이동 기능 활성화.

#### 드롭다운 목록 스타일
- `ListboxProps`: 드롭다운 목록(`Listbox`)에 직접 속성 전달
-    (예: `style` 객체로 `maxHeight`를 지정해 목록이 길어지는 것 방지)

#### 입력 필드 렌더링 및 커스텀 스타일 (renderInput)
- `AutoComplete`의 입력 필드 UI 정의.
- `TextField`와 `sx prop`으로 상세한 디자인 커스텀 적용
- 포커스(Mui-focused)될 때만 border 표시

#### 드롭다운(Popper) 위치 및 동작 커스터마이징 (slotProps)
- `popper`(드롭다운 메뉴) 같은 내부 슬롯의 동작과 위치 제어
- `placement`: 드롭다운 표시 위치 고정 (예: 'bottom-start')
- `modifiers`: `Popper.js`의 고급 동작 수정
- `flip: { enabled: false }`: 화면 공간 부족 시 메뉴가 위로 뒤집히는 동작 비활성화
- `preventOverflow: { enabled: false }`: 메뉴가 화면 경계를 벗어나는 것을 허용. 일관된 위치 유지 시 사용.
  
<br />

# 🔗 References
- [MUI AutoComplete 공식 문서](https://mui.com/material-ui/react-autocomplete/)
