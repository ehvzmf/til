
queryClient.getDefaultOptions().mutations?.onSuccess?.(data, variables, context);
(queryClient.getDefaultOptions().mutations?.onSuccess as any)?.(data, variables, context);

차이점과 해결 원리
1. TypeScript 타입 시스템의 문제
이전 코드에서 TypeScript가 보는 onSuccess의 타입 시그니처는:

하지만 우리가 호출할 때는 3개의 파라미터만 전달:

2. React Query v5의 타입 정의
React Query v5에서 onSuccess 콜백은 실제로 4개의 파라미터를 받습니다:

data: 뮤테이션 결과 데이터
variables: 뮤테이션에 전달된 변수들
context: 컨텍스트 객체
mutateOptions: 뮤테이션 옵션 (선택적)
3. 타입 단언(as any)의 역할
이 부분이 핵심입니다:

as any로 타입 단언을 하면 TypeScript가 타입 체크를 건너뜁니다
따라서 4개 파라미터를 요구하는 타입 체크가 무시됩니다
런타임에서는 여전히 3개 파라미터로 정상 동작합니다
4. 왜 이렇게 해도 동작하는가?
JavaScript에서는 함수에 정의된 것보다 적은 파라미터를 전달해도 문제없이 동작합니다:

5. 더 정확한 해결방법도 있습니다
타입 단언 대신 4번째 파라미터를 명시적으로 전달하는 방법:

하지만 as any 방식이 더 간단하고, 기존 로직을 건드리지 않아서 안전합니다.


---

pnpm build -> 멀쩡
근데 
