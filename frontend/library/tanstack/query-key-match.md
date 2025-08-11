아니요, 정확하지 않습니다. `queryKey: ['all-rank']`로는 부분적인 매칭만 됩니다. 

더 정확하고 효과적인 방법들을 제안드립니다:

````tsx
// 1. 정확한 키 매칭 (추천)
useEffect(() => {
  // 현재 쿼리의 정확한 키로 제거
  queryClient.removeQueries({ 
    queryKey: ['all-rank', selectedPeriod, selectedDate, roleFilter || '', partyFilter || ''] 
  });
  
  if (scrollContainerRef.current) {
    scrollContainerRef.current.scrollTop = 0;
  }
}, [roleFilter, partyFilter, selectedPeriod, selectedDate, queryClient]);

// 2. predicate 함수 사용 (더 유연함)
useEffect(() => {
  // all-rank로 시작하는 모든 쿼리 제거
  queryClient.removeQueries({ 
    predicate: (query) => query.queryKey[0] === 'all-rank'
  });
  
  if (scrollContainerRef.current) {
    scrollContainerRef.current.scrollTop = 0;
  }
}, [roleFilter, partyFilter, queryClient]);

// 3. invalidateQueries 사용 (가장 안전함, 추천)
useEffect(() => {
  // 해당 패턴의 모든 쿼리 무효화
  queryClient.invalidateQueries({ 
    queryKey: ['all-rank'],
    exact: false // 이 옵션으로 부분 매칭 활성화
  });
  
  if (scrollContainerRef.current) {
    scrollContainerRef.current.scrollTop = 0;
  }
}, [roleFilter, partyFilter, queryClient]);
````

**추천하는 최종 수정안:**

````tsx
useEffect(() => {
  // all-rank 관련 모든 쿼리 무효화
  queryClient.invalidateQueries({ 
    queryKey: ['all-rank'],
    exact: false 
  });
  
  if (scrollContainerRef.current) {
    scrollContainerRef.current.scrollTop = 0;
  }
}, [roleFilter, partyFilter, queryClient]);

useEffect(() => {
  queryClient.invalidateQueries({ 
    queryKey: ['all-rank'],
    exact: false 
  });
  if (scrollContainerRef.current) {
    scrollContainerRef.current.scrollTop = 0;
  }
}, [selectedPeriod, selectedDate, queryClient]);
````

`invalidateQueries`를 사용하는 것이 가장 안전합니다. React Query가 자동으로 새 데이터를 다시 가져오기 때문입니다.
