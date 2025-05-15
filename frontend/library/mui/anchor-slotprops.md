> 📅 Date: 2025-05-15

# 📌 Focus

* MUI 드롭다운 메뉴 커스터마이징
* `anchorEl`, `slotProps.paper`, 동작 원리 중심 설명

<br />

# 📝 Learnings

## ✅ MUI Menu는 어떻게 동작할까?

MUI의 `Menu` 컴포넌트는 내부적으로
\*\*열기 기준(anchor)\*\*과 **렌더링 스타일**을 모두 갖춘 **UI 구조 컴포넌트**입니다.

* 메뉴는 단독으로 존재하지 않음 → **어떤 DOM 요소를 기준으로 떠야 함**
* 따라서 이 기준이 되는 DOM 요소가 필요 → 그것이 바로 **`anchorEl`**

<br />

## ✅ anchorEl: 메뉴를 어디에 띄울지를 결정하는 핵심

```tsx
const [anchorEl, setAnchorEl] = useState<null | HTMLElement>(null);

const handleClick = (e: React.MouseEvent<HTMLElement>) => {
  setAnchorEl(e.currentTarget);
};
```

| 개념                         | 설명                   |
| -------------------------- | -------------------- |
| `anchorEl`                 | 메뉴가 열릴 기준이 되는 DOM 요소 |
| `setAnchorEl(null)`        | 메뉴를 닫는 동작            |
| `open = Boolean(anchorEl)` | 메뉴의 열림 여부를 결정        |

📌 즉, `anchorEl`은 "어디에 띄울 것인가"를 지정하는 **위치 anchor**이며,
**드롭다운 메뉴는 anchor 위치에 종속적으로 열린다.**

<br />

## ✅ 왜 slotProps.paper가 필요한가?

기본적인 `Menu` 스타일은 내부에서 **`Paper`** 컴포넌트를 사용해 렌더링됨
→ 외부에서는 이 `Paper`에 직접 접근이 안 됨

그래서 **MUI 5부터 도입된 `slotProps` 구조**를 활용함:

```tsx
<Menu
  slotProps={{
    paper: {
      sx: {
        borderRadius: '12px',
        boxShadow: '...',
        width: 119,
      },
    },
  }}
/>
```

| 키                 | 설명                                |
| ----------------- | --------------------------------- |
| `slotProps.paper` | 내부 드롭다운을 감싸는 Paper 컴포넌트에 props 전달 |
| `sx`              | Paper에 직접 스타일 적용 가능               |

✅ 즉, **`anchorEl`은 위치를 제어**,
**`slotProps.paper`는 그 위치에 뜨는 드롭다운의 스타일을 제어**하는 역할이다.

<br />

## ✅ 이 구조가 왜 중요한가?

| 목적          | 사용 이유                                         |
| ----------- | --------------------------------------------- |
| 드롭다운 위치 제어  | `anchorEl`, `anchorOrigin`, `transformOrigin` |
| 드롭다운 스타일 제어 | `slotProps.paper`                             |
| 사용자 경험 향상   | 정확한 위치 + 정확한 디자인을 동시에 구현                      |

💡 특히 **선택 필터**, **사용자 메뉴**, **날짜 선택기** 같은 UI는
**위치와 디자인을 모두 커스터마이징할 수 있어야 실용적**

<br />

## ✅ 요약

| 요소                                 | 역할                             |
| ---------------------------------- | ------------------------------ |
| `anchorEl`                         | 메뉴가 열릴 위치 기준. 필수               |
| `open`                             | anchorEl 유무에 따라 메뉴 열림 제어       |
| `slotProps.paper`                  | 드롭다운 UI를 감싸는 Paper 스타일을 커스터마이징 |
| `anchorOrigin` / `transformOrigin` | 메뉴 위치 조정 (예: 아래로 뜨게 설정)        |

📌 `anchorEl` 없이 메뉴를 띄우면 **위치가 지정되지 않아 오류**가 발생함.
그래서 클릭된 요소를 anchor로 지정해야 메뉴가 정상적으로 열림.

<br />

# 🔗 References

* [MUI Menu 문서](https://mui.com/material-ui/react-menu/)
* [MUI slotProps 가이드](https://mui.com/material-ui/guides/slot-props/)
* [Popover 구조 이해](https://mui.com/material-ui/react-popover/)
