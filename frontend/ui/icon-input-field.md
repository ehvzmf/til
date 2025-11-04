> 📅 Date: 2025-11-04

# 📌 Focus
- 아이콘이 있는 검색 창 (TextField)
<br />

# 📝 Learnings
```tsx
<TextField
  placeholder='검색어를 입력하세요'
  value={filters.q}
  onChange={(e) => onFilterChange({ ...filters, q: e.target.value })}
  slotProps={{
    input: {
      startAdornment: (
        <InputAdornment position="start">
          <Box component='img' src='/icons/assembly/search.svg' sx={{ width: 18, height: 18 }} />
        </InputAdornment>
      ),
    },
  }}
  sx={{
    width: 438,
    '& .MuiOutlinedInput-root': {
      height: 39,
      borderRadius: '10px',
      backgroundColor: '#F2F5F7',
      '& fieldset': { border: 'none' },
      pl: '13px',
    },
    '& input': {
      fontSize: 13,
      '&::placeholder': {
        color: theme.palette.grey[700],
        opacity: 1,
      },
    },
  }}
/>
```
- (배운 내용 간략 정리)
- (코드 예제 및 설명 추가 가능)
<br />

# 🔗 References
- [관련 링크](URL)
