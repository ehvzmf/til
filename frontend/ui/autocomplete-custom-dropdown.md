> � Date: 2025-10-22

# 📌 Focus
- MUI Autocomplete 커스텀 컴포넌트
- 검색 가능한 드롭다운 셀렉트
- TypeScript 타입 안전성
- 사용자 경험 최적화
<br />

# 📝 Memo

## AutocompleteSelect 컴포넌트
> 검색 기능이 있는 드롭다운 셀렉트 박스 <br />
> MUI의 Autocomplete를 기반으로 커스터마이징 <br />
> **옵션이 많은 경우** 추천 

<br />

## Advantages

### 1. 사용자 경험 개선
- **빠른 검색**
- **스크롤 피로도 감소**
- **오타 허용**: 부분 일치로 검색되어 정확한 철자를 몰라도 찾을 수 있음

### 2. 접근성 향상
- **키보드 네비게이션**: 마우스 없이도 화살표 키로 옵션 탐색 가능
- **스크린 리더 지원**: ARIA 속성으로 접근성 도구와 호환
- **포커스 관리**: 자동으로 포커스가 관리되어 키보드 사용자에게 친화적

### 3. 성능 최적화
- **가상화**: 많은 옵션도 성능 저하 없이 처리
- **지연 로딩**: 필요할 때만 옵션 목록을 렌더링

## 컴포넌트 Props 체크

### 기본 Props
```tsx
interface AutocompleteSelectProps {
  label?: string;           // 라벨 텍스트
  labelWidth?: number | string; // 라벨 영역 너비 (기본: 92px)
  required?: boolean;       // 필수 입력 표시 (*) 
  options: OptionItem[];    // 선택 가능한 옵션 배열
  value: string;           // 현재 선택된 값 (id)
  onChange: (event: { target: { value: string } }) => void; // 값 변경 핸들러
  placeholder?: string;     // 플레이스홀더 텍스트
  valueWidth?: number;     // 입력 필드 너비 (기본: 294px)
  disabled?: boolean;      // 비활성화 상태
  sx?: SxProps<Theme>;     // 추가 스타일링
}
```

### OptionItem 타입 구조
```tsx
type OptionItem = {
  id: string;    // 고유 식별자 (실제 저장되는 값)
  name: string;  // 화면에 표시되는 텍스트
}
```

## 핵심 기능 상세 분석

### 1. 값 관리 로직
```tsx
const selectedOption = options.find(opt => opt.id === value);
const displayValue = selectedOption?.name || '';
```
- `value` prop은 `id`를 받지만, 화면에는 `name`을 표시
- 내부적으로 ID와 이름을 매핑하여 관리

### 2. 검색 및 선택 처리
```tsx
// 드롭다운에서 선택할 때
onChange={(_, newValue) => {
  const option = options.find(opt => opt.name === newValue);
  onChange({ target: { value: option?.id || '' } });
}}

// 직접 타이핑할 때
onInputChange={(_, newInputValue) => {
  const option = options.find(opt => opt.name === newInputValue);
  if (option) {
    onChange({ target: { value: option.id } });
  }
}}
```
- 사용자가 선택하면 `name`으로 찾아서 `id`를 반환
- 표준 HTML input과 동일한 인터페이스 제공 (`{ target: { value } }`)

### 3. 비활성화 로직
```tsx
const isDisabled = disabled || options.length === 0;
```
- 명시적 비활성화 또는 옵션이 없을 때 자동 비활성화

## MUI Autocomplete 커스터마이징 상세

### 1. Popper 설정 (드롭다운 위치 제어)
```tsx
slotProps={{
  popper: {
    placement: 'bottom-start',  // 아래쪽 왼쪽 정렬
    modifiers: [
      {
        name: 'flip',
        enabled: false,         // 공간 부족 시 위로 뒤집기 비활성화
      },
      {
        name: 'preventOverflow',
        enabled: true,          // 화면 밖으로 나가는 것 방지
      },
    ],
  },
}}
```

### 2. Paper 스타일링 (드롭다운 목록 스타일)
```tsx
paper: {
  sx: {
    backgroundColor: '#fff',
    maxHeight: 300,           // 최대 높이 제한
    marginTop: '7px',         // 입력 필드와 간격
    boxShadow: '...',         // 커스텀 그림자
    '& .MuiAutocomplete-listbox': {
      maxHeight: 300,
      '& .MuiAutocomplete-option': {
        fontSize: 17,          // 옵션 텍스트 크기
        fontWeight: 400,
        color: '#000',
      }
    }
  }
}
```

### 3. TextField 커스터마이징
```tsx
sx={{
  '& .MuiOutlinedInput-root': {
    height: 60,                    // 입력 필드 높이
    backgroundColor: '#fff',
    borderRadius: '4px',
    border: `1px solid ${theme.palette.grey[400]}`,
    paddingLeft: '20px !important',
    paddingRight: '10px !important',
    '& fieldset': {
      border: 'none',              // 기본 테두리 제거
    },
    '&:hover fieldset': {
      border: 'none',              // 호버 시 테두리 제거
    },
    '&.Mui-focused': {
      border: `1px solid ${theme.palette.grey[400]}`,
      '& fieldset': {
        border: 'none',            // 포커스 시에도 커스텀 테두리 유지
      }
    },
  },
}}
```

## 확장 가능한 추가 Props

### 1. 검색 관련 개선
```tsx
interface ExtendedProps extends AutocompleteSelectProps {
  // 검색 최소 글자 수
  minSearchLength?: number;
  
  // 검색 지연 시간 (디바운싱)
  searchDelay?: number;
  
  // 대소문자 구분 없이 검색
  caseInsensitive?: boolean;
  
  // 부분 일치 vs 시작 문자 일치
  matchFrom?: 'start' | 'any';
  
  // 커스텀 필터 함수
  filterOptions?: (options: OptionItem[], inputValue: string) => OptionItem[];
}
```

### 2. UI/UX 개선
```tsx
interface UIExtendedProps extends AutocompleteSelectProps {
  // 로딩 상태 표시
  loading?: boolean;
  
  // 옵션이 없을 때 메시지
  noOptionsText?: string;
  
  // 그룹핑 지원
  groupBy?: (option: OptionItem) => string;
  
  // 옵션 렌더링 커스터마이징
  renderOption?: (props: any, option: OptionItem) => React.ReactNode;
  
  // 아이콘 표시
  startAdornment?: React.ReactNode;
  endAdornment?: React.ReactNode;
  
  // 에러 상태
  error?: boolean;
  helperText?: string;
}
```

### 3. 데이터 처리 개선
```tsx
interface DataExtendedProps extends AutocompleteSelectProps {
  // 다중 선택 지원
  multiple?: boolean;
  
  // 비동기 옵션 로딩
  asyncOptions?: (inputValue: string) => Promise<OptionItem[]>;
  
  // 새 옵션 생성 허용
  freeSolo?: boolean;
  
  // 옵션 생성 핸들러
  onCreateOption?: (inputValue: string) => void;
  
  // 값 변환 함수
  getOptionValue?: (option: OptionItem) => string;
  getOptionLabel?: (option: OptionItem) => string;
}
```

## 실제 사용 예시

### 기본 사용법
```tsx
const [selectedPolitician, setSelectedPolitician] = useState('');

<AutocompleteSelect
  label="정치인 선택"
  required
  options={politicians}
  value={selectedPolitician}
  onChange={(e) => setSelectedPolitician(e.target.value)}
  placeholder="정치인을 검색하세요"
/>
```

### React Hook Form과 연동
```tsx
const { register, setValue, watch } = useForm();

<AutocompleteSelect
  label="지역 선택"
  options={regions}
  value={watch('region')}
  onChange={(e) => setValue('region', e.target.value)}
  placeholder="지역을 선택하세요"
/>
```

### 동적 옵션 로딩
```tsx
const [options, setOptions] = useState<OptionItem[]>([]);
const [loading, setLoading] = useState(false);

useEffect(() => {
  const loadOptions = async () => {
    setLoading(true);
    const data = await fetchPoliticians();
    setOptions(data);
    setLoading(false);
  };
  loadOptions();
}, []);

<AutocompleteSelect
  options={options}
  disabled={loading}
  placeholder={loading ? "로딩 중..." : "선택하세요"}
  // ... 기타 props
/>
```

## 성능 최적화 팁

### 1. 대용량 데이터 처리
```tsx
// 가상화 활용 (react-window 등)
import { FixedSizeList } from 'react-window';

const virtualizedOptions = useMemo(() => {
  // 옵션이 1000개 이상일 때만 가상화 적용
  return options.length > 1000 ? 
    <VirtualizedList options={options} /> : 
    options;
}, [options]);
```

### 2. 검색 디바운싱
```tsx
const [searchTerm, setSearchTerm] = useState('');
const debouncedSearchTerm = useDebounce(searchTerm, 300);

// 디바운싱된 검색어로 필터링
const filteredOptions = useMemo(() => {
  if (!debouncedSearchTerm) return options;
  return options.filter(option => 
    option.name.toLowerCase().includes(debouncedSearchTerm.toLowerCase())
  );
}, [options, debouncedSearchTerm]);
```

### 3. 메모이제이션 활용
```tsx
const AutocompleteSelect = React.memo(({
  // props
}) => {
  // 컴포넌트 구현
}, (prevProps, nextProps) => {
  // 커스텀 비교 로직
  return prevProps.options === nextProps.options && 
         prevProps.value === nextProps.value;
});
```

## 접근성 개선 사항

### 1. ARIA 레이블링
```tsx
<Autocomplete
  aria-label={label || "선택 옵션"}
  aria-describedby={helperText ? `${id}-helper-text` : undefined}
  // ... 기타 props
/>
```

### 2. 키보드 네비게이션 강화
```tsx
// Enter로 드롭다운 열기
onKeyDown={(e) => {
  if (e.key === 'Enter' && !open) {
    setOpen(true);
  }
}}
```

<br />

# 🔗 References
- [MUI Autocomplete API](https://mui.com/material-ui/api/autocomplete/)
- [MUI TextField Customization](https://mui.com/material-ui/customization/how-to-customize/)
- [Popper.js Modifiers](https://popper.js.org/docs/v2/modifiers/)
- [ARIA Autocomplete Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/)
- [React Hook Form Integration](https://react-hook-form.com/get-started#IntegratingwithUIlibraries)

