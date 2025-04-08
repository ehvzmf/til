> 2025-04-08

# 📌 Focus
> WYSIWYG
> what you see is what you get

<br />

# 📝 Learnings
- 사용자가 보는 것이 그대로 최종 산물에 나타나도록 하는 유저 인터페이스
- desktop-based > Adobe Dreamweaver, Amaya (OS 상에서 동작)
- JavaScript-based > Editor.js, TinyMCE, FCKeditor, Quill, Tiptap

### 🥊 Quill vs Tiptap
- react-quill은 기존 프로젝트에서 사용하던 라이브러리
- 새로 찾아본 결과 다양한 프레임워크를 지원하고 UI가 예쁜 Tiptap이 땡김 (복잡한 기능은 필요x, 커스텀은 필요)
<br />

| 항목 | **Tiptap** | **Quill / React-Quill** |
|------|------------|--------------------------|
| 🛠 기반 | [ProseMirror](https://prosemirror.net/) | [Quill.js](https://quilljs.com/) |
| 📦 React 지원 | 공식 지원 (hooks + extensions) | React-Quill이 래퍼 라이브러리로 제공 |
| 🧱 확장성 | **아주 높음** (모듈 단위 확장 가능) | 제한적 (기본 툴바 외엔 커스터마이징 어려움) |
| 🎨 커스터마이징 | DOM, 노드, 키보드, 명령어, 입력 규칙 등 매우 세밀하게 제어 가능 | 툴바 설정 정도만 제어 가능 |
| 🎯 출력 포맷 | JSON(Node Tree), HTML, Markdown (자유 변환 가능) | Delta(Quill 포맷), HTML |
| 🧩 플러그인 구조 | 완전한 플러그인 기반 (Mention, Table, Task list 등 플러그인 풍부) | 커스텀 핸들러는 있음, 공식 플러그인은 적음 |
| 📚 문서화 | **매우 상세하고 모던함** | 기본적인 문서만 제공, React 문서 부족 |
| 🔐 XSS 보안 | 직접 필터링 필요 (프로세스 제어 가능) | 기본 XSS 대응은 적절하지만 구조 제한 |
| 🚀 SSR 대응 | **잘 됨** (Next.js 등에서 호환성 좋음) | SSR 환경에서 에러 발생 쉬움 (동적 import 필요) |
| 🎯 사용 목적 | **복잡하고 자유로운 에디터** (CMS, Wiki, Notion형 UI 등) | **단순한 WYSIWYG** (댓글, 블로그, 이메일 등) |
| ⚠️ 러닝커브 | 약간 있음 (초반 설정 필요) | 매우 쉬움 (설치만으로 바로 사용 가능) |


### 📌 요약
---
- 📰 **Quill** → 블로그 글쓰기, 게시판, 댓글 등  
- 🗃 **Tiptap** → Notion/Confluence 스타일 에디터, 드래그·드롭 지원, 멘션, Table, 커스텀 블록 등
---
