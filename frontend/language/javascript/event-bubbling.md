> 📅 Date: 2025-08-26

# 📌 Focus
- **JavaScript Event Bubbling**
- **React Event Handling**
<br />

# 📝 Learnings

## 🔍 발생한 문제 상황
- 프로필 목록이 있는 모달
- 프로필 박스를 클릭하면 선택된 프로필에 대해 처리 (이미 onClick 핸들러 존재)
- 내부 프로필 사진 영역에 별도의 클릭 핸들러를 추가하고 싶음
- 클릭하면 투표되지만, 프로필 카드를 보여주고 싶기 때문에.
- 하지만, 두 핸들러가 모두 실행되는 문제가 예상
- 투표 핸들러를 제한할 수도 있지만, 모바일 사용성이 저해될 우려 존재

## ⚠️ 초기 코드의 문제점
```tsx
<Flex onClick={onClick}>  {/* 투표 선택 */}
  <Avatar onClick={handleProfileOpen} />  {/* 프로필 카드 열기 */}
</Flex>
```
이 구조에서는 Avatar 클릭 시 두 이벤트가 순차적으로 실행 (일단 보기엔 괜찮지만, 장기적으로 버그가 발생될 수 있음)

## 🎯 이벤트 버블링 개념
**이벤트 버블링(Event Bubbling)**은 DOM 요소에서 이벤트가 발생했을 때, 해당 요소부터 시작해서 부모 요소들을 거슬러 올라가며 동일한 타입의 이벤트 핸들러를 순차적으로 실행하는 JavaScript의 기본 동작

### 🔄 이벤트 전파 과정
```
Child Element Click → Parent Element Click → Grandparent Element Click
```

### 💥 문제가 되는 이유
1. **의도하지 않은 동작**: 자식 요소만 클릭했는데 부모의 핸들러도 실행
2. **사용자 경험 저하**: 예측하지 못한 결과로 혼란 야기
3. **로직 충돌**: 서로 다른 목적의 핸들러들이 동시 실행

### 🌟 다양한 활용 사례
```tsx
// Case 1: 모달 외부 클릭으로 닫기
<div onClick={closeModal}>  {/* 배경 */}
  <div onClick={(e) => e.stopPropagation()}>  {/* 모달 내용 */}
    모달 내용
  </div>
</div>

// Case 2: 카드 내부의 버튼들
<div onClick={selectCard}>  {/* 카드 선택 */}
  <button onClick={(e) => { e.stopPropagation(); editCard(); }}>수정</button>
  <button onClick={(e) => { e.stopPropagation(); deleteCard(); }}>삭제</button>
</div>

// Case 3: 리스트 아이템과 액션 버튼
<li onClick={viewDetail}>  {/* 상세보기 */}
  <button onClick={(e) => { e.stopPropagation(); toggleFavorite(); }}>♥</button>
</li>
```

## ✅ 적용한 해결책
```tsx
<Avatar
  onClick={(e) => {
    e.stopPropagation();  // 이벤트 버블링 차단
    useProfileCardStore.getState().open(politician.politician_id);
  }}
  sx={{ cursor: 'pointer' }}  // 시각적 피드백 추가
/>
```

`stopPropagation()` 메서드를 사용해 이벤트가 부모 요소로 전파되는 것을 차단

## 🛠️ 추가 활용 방안

### 1. **preventDefault()와의 조합**
```tsx
<a href="/link" onClick={(e) => {
  e.preventDefault();      // 기본 동작(페이지 이동) 차단
  e.stopPropagation();     // 부모 이벤트 차단
  customAction();
}}>
```

### 2. **이벤트 위임 패턴**
```tsx
// 부모에서 이벤트를 처리하되, 특정 자식은 제외
<ul onClick={(e) => {
  if (e.target.tagName === 'BUTTON') return;  // 버튼은 제외
  handleListItemClick(e);
}}>
  <li>아이템 1 <button>액션</button></li>
  <li>아이템 2 <button>액션</button></li>
</ul>
```

### 3. **조건부 이벤트 차단**
```tsx
<div onClick={parentHandler}>
  <button onClick={(e) => {
    if (someCondition) {
      e.stopPropagation();  // 특정 조건에서만 차단
    }
    childHandler();
  }}>
```

### 4. **React에서의 주의사항**
React는 SyntheticEvent를 사용하므로, 네이티브 이벤트와 다를 수 있습니다:
```tsx
// React SyntheticEvent
onClick={(e) => e.stopPropagation()}

// 네이티브 이벤트 접근이 필요한 경우
onClick={(e) => e.nativeEvent.stopImmediatePropagation()}
```

<br />

# 🔗 References
- [MDN - Event.stopPropagation()](https://developer.mozilla.org/en-US/docs/Web/API/Event/stopPropagation)
- [React SyntheticEvent 공식 문서](https://react.dev/reference/react-dom/components/common#react-event-object)
- [JavaScript Event Bubbling과 Capturing](https://javascript.info/bubbling-and-capturing)
