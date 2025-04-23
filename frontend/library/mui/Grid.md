> 📅 Date: 2025-04-07

# 📌 Focus
Material UI: Grid
<br />

# 📝 Learnings
- v.6.4까지만 해도 Grid2를 사용했는데, (지난 프로젝트)
![image](https://github.com/user-attachments/assets/25c7b301-4259-4662-bb11-956bee41c83a)

- v.7부터는 그냥 Grid를 쓴다.
```
import { Grid } from '@mui/material';

<Grid container spacing={2}>
  <Grid size={8}>
    <Item>size=8</Item>
  </Grid>
  <Grid size={4}>
    <Item>size=4</Item>
  </Grid>
  <Grid size={4}>
    <Item>size=4</Item>
  </Grid>
  <Grid size={8}>
    <Item>size=8</Item>
  </Grid>
</Grid>
```
- `import`도 그냥 Grid (기존 그리드는 `@/mui/material/GridLegacy`)
- `item`, `zeroMinWidth` 삭제
- xs, sm, md ... 직접 지정하지 않고 size props 안에 적용
- `<Grid size="grow">`로 변경 (true 대신)
- negative margin 삭제, overflow 방지
- Nested depth 제한 없음
<br />

# 🔗 References
[MUI Grid](https://mui.com/material-ui/react-grid/)
