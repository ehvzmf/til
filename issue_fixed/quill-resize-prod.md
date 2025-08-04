> 📆 2025-08-04
>

- 게시판에서 글 작성화면으로 이동 시 오류 발생

```
Unexpected Application Error!
Cannot assign to read only property 'Symbol(Symbol.toStringTag)' of object '[object Module]'
TypeError: Cannot assign to read only property 'Symbol(Symbol.toStringTag)' of object '[object Module]'
```

Quill 모듈 등록 과정에서 발생하는 문제예요! 프로덕션 빌드에서 모듈이 read-only로 처리되면서 발생
- 동적 import 로 안전한 모듈 등록 시도
- 모듈 로드 완료까지 대기
- 조건부 모듈 설정

- 하지만 여전히 프로덕션 환경에서는 문제 발생
- 결국 완전히 제거하고 다른 모듈 사용
- 
