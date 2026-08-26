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
		// 		- 캐시 hit(이전 페이지 복귀): 노드가 이미 준비되어 즉시 복원
		//		- 캐시 miss(처음 가는 페이지): 노드가 없어 rAF로 미뤄지고, 데이터 로드 후 onModelUpdated에서 최종 복원
		restoreSelection();
	}
	if (params.newPageSize) {
		setTimeout(() => params.api.refreshServerSide({ purge: true }), 0);
	}
},

// 캐시 miss 전용: 데이터 로드 후 모델 갱신 시 발화
onModelUpdated: () => {
	restoreSelection();
},
```

`restoreSelection()`의 복원 로직 (개념적):
```js
const restoreSelection = useCallback(() => {
	// 외부 ref에 "사용자가 의도적으로 선택한 행"의 id 목록을 보관한다.
	// ag-grid 선택 상태와는 별개로, 페이지를 오가며 살아남아야 하는 출처.
	const selectIds = new Set(selectedRef.current.map(/* id getter */));

	const applyRestore = () => {
		const rendered = api.getRenderedNodes();
		if (!rendered.length) return false; // 노드 미준비 -> 호출 측에서 rAF 재시도
		rendered.forEach((node) => {
			if (!node.data) return;
			const shouldBe = selectedIds.has(node.data.id);
			if (node.isSelected() !== shouldBe) {
				// source='api'로 지정 -> 사용자 선택과 구분해 무한 루프/오염 방지
				node.setSelected(shouldBe, false, 'api');
			}
		});
		return true;
	};
	if (!applyRestore()) requestAnimationFrame(applyRestore);
}, [gridRef]);
```

## 왜 두 이벤트에 같은 로직을 걸어도 안전한가 (idempotent)
`restoreSelection()`은 `isSelected() !== shouldBe`일 때만 `setSelected`를 호출한다. **이미 맞은 상태면 아무것도 안 한다.** 그래서:
- 캐시 hit: `onPaginationChanged`에서만 복원 -> OK
- 캐시 miss: `onPaginationChanged`(rAF 미룸) + `onModelUpdated` -> 한쪽만 실제 효과, 다른 쪽은 no-op
이 idempotent 성질 덕분에 "캐시 hit/miss를 미리 판별"하는 분기를 만들 필요 없이 **양쪽 경로에 같은 복원 로직을 걸어두는** 단순한 구조로 잡을 수 있다.

## 자동 선택 이벤트 무시 (오염 방지)
`onSelectionChanged` 핸들러는 **사용자 의도**만 반영해야 한다. 그런데
`deselectAll()` / `setSelected(..., 'api') / 페이지 전환으로 행이 교체될 때
ag-grid가 자동으로 `onSelectionChanged`를 발화시킨다. 이걸 그대로 처리하면: 

- `deselectAll()` -> "사용자가 전체 해제"로 오인 -> ref가 비워짐 -> 복원 불가
- 페이지 전환 -> carry over된 선택이 "사용자 선택"으로 ref에 섞임

그래서 source로 필터링한다:

```js
const handleSelectionChanged = (params) => {
	// 'api': restoreSelection/deselectAll 등 프로그램 호출
	// 'rowDataChanged': 페이지 전환으로 행이 교체될 때 ag-grid 내부 발화
	if (params.source === 'api' || params.source === 'rowDataChanged') return;
	// ...이하 사용자 실제 선택만 ref에 반영 
```

> `setSelected(value, clearSelection, source)`의 세 번째 인자로 `'api'`를
> 넘기면, 그 호출에서 발화하는 `onSelectionChanged`의 `params.source`가
> `'api'`로 오게 된다.

## 출처 분리: ref vs ag-grid 상태
페이지를 오가며 살아남아야 하는 "선택 목록"은 **ag-grid의 선택 상태와
분리된 별도 저장소(ref)**에 보관한다. ag-grid 선택 상태는 페이지 전환/
`deselectAll()`등으로 언제든 리셋될 수 있기 때문.

복원 흐름:
```
ref(신뢰 출처) --복원-- -> ag-grid 노드 선택 상태 (표시용)
ag-grid 사용자 선택 --(source 필터 후)-- -> ref(누적)
```

## ag-grid v35 SSRM 선택 관련 함정 정리

1. **`onModelUpdated`는 캐시 hit에서 안 불린다.** 페이지 전환 후 반드시 뭔가 해야 한다면 `onPaginationChanged`에도 걸어야 한다.
2. **전체 선택 상태가 새 페이지로 carry over된다.** `deselectAll()`을 새 페이지 그리기 전에 미리 쳐야 깜박임이 없다.
3. **`selectAll: 'currentPage'`는 SSRM에서 워닝을 낸다.** (clientSide row model만 정식 지원) 그러나 이 옵션이 있어야 헤더 체크박스가 '전체 페이지'가 아니라 '현재 페이지만' 토글한다. 제거하면 기본값 `'all'`로 바뀌어 동작이 깨지므로 워닝을 감수한다.
4. **`getRowId`는 SSRM 선택 유지에 필수.** 페이지 전환 시 id 기준으로 노드를 식별해 선택을 올바르게 유지/복원한다. (없으면 warning #188)
5. **`setSelected(value, clear, source)`의 source 인자**로 프로그램 선택과 사용자 선택을 구분할 수 있다. `onSelectionChanged`에서 source로 필터링해 자동 이벤트를 무시하지 않으면 ref 오염/무한루프 발생
6. **헤더 체크박스 상태(checked/empty/indeterminate)는 ag-grid가 자동 관리.** 현재 렌더링된 행 기준. 페이지 전환 후 ref 매칭이 부분적이면 indetermination가 되는데, 이는 정상 동작
(나는 empty로 수정하긴 함)

## Debugging Tip
- 처음엔 `console.debug`로 각 이벤트(`onPaginationChanged`, `onModelUpdated`, `onSelectionChanged`의 `params.source`, `getRenderedNodes().length`, ref 카운트)를 찍으며 흐름 확인
- 캐시 hit/miss를 구분하려면 `onModelUpdated`가 발화하는지 여부를 로그로 체크. "복귀했는데 `onModelUpdated`가 안 찍힌다" = hit
- 복원이 안되면 어느 이벤트에만 복원 로직이 걸려 있는지 먼저 의심.

## 핵심 교훈
> SSRM에서 "이벤트 A에만 로직을 걸었더니 캐시 hit/miss에 따라 A가 안 불리는 케이스가 있었다. 
> 타이밍 의존 로직은 항상 발화하는 이벤트(여기서는 `onPaginationChanged`)를 기본 트리거로 쓰고,
> 보조 이벤트(`onModelUpdated`)는 idempotent하게 중복으로 걸어 blind spot을 메우자.

<br />

# 🔗 References
- [관련 링크](URL)
