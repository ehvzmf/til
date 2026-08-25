> 📅 Date: 2026-08-25

# 📌 Focus
- AG GRID v35 기준 SSRM 테이블에서 선택된 항목 유지
<br />

# 📝 Learnings
> c.f. ag-grid
> 데이터를 그리드에 올리는 방식(row model)을 여러 개 제공한다.
> | Row Model | desc | 선택 복원 난이도 | 
> |---|---|---|
> | **CSRM** | 행을 한 번에 메모리에 올려 ag-grid가 페이징/필터/정렬 처리 | 쉬움 |
> | **Infinite** | 무한 스크롤. 행을 블록 단위로 지연 로드 | 중간 |
> | **SSRM** | 페이징/필터/정렬/블록 로드를 서버에 위임. 행은 페이지(블록) 단위로만 메모리에 있고 나머지는 캐시/요청으로 관리 | **어려움** |

## ISSUE
**ag-grid SSRM + 체크박스 선택 유지 (캐시 hit/miss 타이밍)
> Server-Side Row Model(SSRM)에서 페이지 전환 시 체크박스 선택을 유지
> UI 깜박임 개선

### SSRM 동작 방식 (페이징 기준)
- `datasource.getRows(params)` 콜백을 구현해, ag-grid가 요청한 `startRow`/`endRow` 범위에 맞춰 서버에서 행을 가져와 `params.successCallback(rows, lastRowIndex)`로 넘긴다.
- ag-grid는 받은 페이지(블록)를 **내부 캐시에 보관**한다.
  `cacheBlockSize`로 블록 크기를, `maxBlocksInCache`로 캐시 유지량 조절
- 페이징은 블록 단위와 1:1 매핑이 보통 (convention)
- 사용자가 페이지를 넘기면 -> 캐시에 있으면 재사용, 없으면 `getRows` 호출

### selection 관련 핵심 API/EVENT
| 이름 | 종류 | 의미 | 
|---|---|---|
| `rowSelection.mode` | option | `'single'` / `'multiRow'` etc. |
| `rowSelection.selectAll` | option | 헤더 체크박스가 선택하는 범위. `'all'`(전체 페이지)/`'currentPage'`(현재 페이지만). **v35에서 SSRM은 `'currentPage'`를 정식 지원하지 않아 warning #195 발생 |
| `getRowId(params)` | option | 행의 고유 id getter. SSRM에서 페이지 전환 시 선택 유지/복원에 필수 (없으면 warning #188) |
| `api.deselectAll` | API | 현재 선택 모두 해제. `source='api'`로 발화 |
| `node.setSelected(value, clearSelection, source) | API | 단일 노드 선택 토글. 세 번째 `source`인자로 `'api'` 지정 시 `onSelectionChanged`의 `params.source`가 `'api'`로 옴 |
| `api.getRenderedNodes()` | API | 현재 화면에 렌더된 노드만 반환. SSRM에서 "현재 페이지 행"을 다룰 때 핵심 (전체 행은 메모리에 없으므로) |
| `onSelectionChanged` | event | 선택 변경 시 바ㄹ화. `params.source`로 원인 구분. `'ui'`(사용자 클릭)/`'api'`(프로그램 호출)/`'rowDataChanged'`(행 교체 등 내부) | 
| `onPaginationChanged` | event | 페이지 인덱스/사이즈 변ㄴㅋ경 시 항상 발화 (캐시 hit/miss와 무관) |
| `onModelUpdated` | event |  행 모델이 실제로 갱신될 때 발화. **캐시 hit에서는 발화하지 않음** |

### 왜 SSRM에서 선택 관리가 어려운가
[!NOTE] CSRM은 행이 항상 메모리에 있어 ag-grid가 선택을 알아서 유지하지만, **SSRM은 페이지(블록)단위로만 행이 메모리에 있고, 캐시 hit/miss에 따라 발화하는 이벤트가 달라져** 타이밍 의존 복원 로직을 직접 짜야 한다.
이 배경을 바탕으로 아래 실제 구현/문제 해결을 본다. 

## 요구사항
페이징이 적용된 서버사이드 테이블. 사용자가 페이지를 오가며 여러 행을 체크박스로 선택한다. 
1. 1페이지에서 선택 -> 2페이지로 이동 -> 다시 1페이지로 돌아와도 선택된 행의 체크박스가 초기화되지 않고 유지될 것
2. 페이지 전환 시 전체 선택 UI가 깜박이지 않을 것 
3. "추가된 목록" 패널에 선택된 행이 누적되어 개수가 보일 것

## 핵심 개념: SSRM 캐시 hit/miss
SSRM은 `datasource.getRows()`로 한 번 불러온 페이지를 **내부 캐시에 보관**한다.
같은 페이지로 다시 가면 서버 요청 없이 캐시된 행을 그대로 그린다.

이때 어떤 **ag-grid 이벤트가 발화하느냐**가 달라진다:
| tkdghkd | `onPaginationChanged` | `onModelUpdated` | 행 로드 |
|---|:---:|:---:|---|
| 처음 가는 페이지 (캐시 miss) | 발화 | **발화** | `getRows()` 호출 |
| 캐시된 페이지로 복귀 (캐시 hit) | 발화 | **미발화** | 캐시 사용, 요청 없음 |

- **`onPaginationChanged`**: 페이지 인덱스/사이즈가 바뀔 떄마다 항상 발화. 캐시 hit/miss와 무관
- **`onModelUpdated`**: 행 모델이 실제로 갱신될 떄 발화. **캐시 hit에서는 행이 다시 로드되지 않으므로 발화하지 않는다.** <- 이게 함정의 핵심.

> 즉 "복원 로직을 `onModelUpdated`에만 걸어두면, 캐시 hit 페이지에서는 복원이 아예 안 불린다."

## carry over issue
ag-grid는 페이지 전환 시 **이전 페이지의 "전체 선택(헤더 체크박스)" 상태를 새 페이지 행으로 그대로 옮겨버린다** (v35 SSRM 기준) 이로 인해:
- 1페이지에서 헤더 체크박스로 전체 선택
- 2페이지 진입 시 2페이지 행이 잠깐 "전체 선택"으로 렌더
- 이후 복원 로직이 deselect 하면서 사라짐 -> **1프레임 깜박임**

"사후 보정(이미 그려진 전체 선택을 나중에 지움)" 구조라서 깜박임 발생.

## 해결: "페이지 전환 시작 시점"에 비우고 + 복원
복원을 `onModelUpdated` 하나에만 의존하지 않고, **페이지 전환 자체가 트리거인 `onPaginationChanged` 시점**에 `deselectAll()` + `restoreSelection()` 둘 다 처리한다.

```js
onPaginationChanged: (params) => {
	if (params.newPage && params.api) {
		// 1) 새 페이지 데이터가 그려지기 전에 carry over된 전체 선택을 비운다.
		// 		-> 깜박임 원천 제거 (사후 보정이 아님)
		params.api.deselectAll();

		// 2) 같은 시점에 즉시 복원
		// 		- 

onModelUpdated: () => {
	restoreSelection();
},
```

`restoreSelection()`의 복원 로직 (개념적):
```js
```




<br />

# 🔗 References
- [관련 링크](URL)
