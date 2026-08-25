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






<br />

# 🔗 References
- [관련 링크](URL)
