> 📅 Date: 2025-04-08

# 📌 Focus
Material UI v7 upgrade
<br />

# 📝 Learnings
### Improvements
- ESM 지원 개선 (번들러 문제 해결)
- CSS 레이어 지원/슬롯 패턴 표준화/불필요한 API 제거

### Breaking Changes
- deep import 비허용
- Grid(Legacy), Grid2 이름 변경
- InputLabel의 size prop 표준화
- SvgIcon의 data-testid 제거됨
- 테마 동작 변경
  - 다크/라이트 모드 변경 시, theme 객체는 재생성되지 않음
  - 스타일 작성 시 theme.palette.xxx 대신 theme.vars.palette.xxx 사용 권장
  - 필요시 ThemeProvider forceThemeRerender로 기존 방식 유지 가능
- deprecated API 제거
<br />

# 🔗 References
- [Upgrade to v7](https://mui.com/material-ui/migration/upgrade-to-v7/)
