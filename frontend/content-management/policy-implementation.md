> 📅 Date: 2025-08-07

# 📌 Focus
- Legal text 관리 전략 (constants vs JSON vs CMS)
- Long text UI 패턴 (Modal vs Accordion vs 별도 페이지)
- 고려사항 (반응형, 스크롤, 로딩)

<br />

> 😰 Pain Points
- 냅다 화면에 Typography로 다 넣어버리긴 좀...
- 텍스트 관리 방법을 모르겠음
- 단순 md 파일로 넣자니 스타일 구현이 힘듬
- 변경될 수 있는 내용이니 확장 가능한 구조로 만들어야 함 

<br /> 

# 📝 Learnings
- (배운 내용 간략 정리)



## 파일 관리 

1. 별도 파일로 분리 (가장 일반적)
javascript// constants/privacyPolicy.js
export const PRIVACY_POLICY = {
  section1: {
    title: '수집하는 개인정보 항목',
    content: `회사는 서비스 제공을 위해...`
  },
  section2: {
    title: '개인정보 수집 및 이용 목적',
    content: `수집된 개인정보는...`
  }
  // ...
};

// 또는
export const PRIVACY_POLICY_TEXT = `
1. 수집하는 개인정보 항목
회사는 서비스 제공을 위해...

2. 개인정보 수집 및 이용 목적
...
`;
2. JSON 파일로 관리
json// public/data/privacy-policy.json
{
  "lastUpdated": "2025-01-15",
  "sections": [
    {
      "id": "collect-info",
      "title": "수집하는 개인정보 항목",
      "content": "회사는 서비스 제공을 위해..."
    }
  ]
}
3. CMS/Headless CMS 사용 (대기업)
javascript// Contentful, Strapi, Notion API 등
const fetchPrivacyPolicy = async () => {
  const response = await fetch('/api/content/privacy-policy');
  return response.json();
};
4. Markdown 파일 (개발자 친화적)
markdown<!-- docs/privacy-policy.md -->
# 개인정보처리방침

## 1. 수집하는 개인정보 항목
회사는 서비스 제공을 위해...
실무에서 가장 많이 쓰는 방식:
스타트업/중소기업 (90%)
javascript// constants/legalTexts.js - 한 파일에 모든 법적 텍스트
export const LEGAL_TEXTS = {
  privacyPolicy: '...',
  termsOfService: '...',
  cookiePolicy: '...'
};
중견기업 (70%)
javascript// 각각 별도 파일
// constants/privacyPolicy.js
// constants/termsOfService.js
// utils/textManager.js (공통 로직)
대기업 (50%)

CMS 사용 (비개발자도 수정 가능)
버전 관리 시스템
다국어 지원

실무 팁들:
javascript// 1. 날짜 포함 (법적 요구사항)
export const PRIVACY_POLICY = {
  lastUpdated: '2025-01-15',
  version: '2.1',
  content: '...'
};

// 2. 환경별 분리
const PRIVACY_POLICY = process.env.NODE_ENV === 'production' 
  ? PRIVACY_POLICY_PROD 
  : PRIVACY_POLICY_DEV;

// 3. 로딩 최적화 (큰 텍스트용)
const PrivacyPolicy = lazy(() => import('./constants/privacyPolicy'));
결론: 대부분은 그냥 constants 폴더에 JS 파일로 관리

<br /> 

## UI patterns
1. Modal/Dialog 방식 (가장 일반적)
jsx// 링크 클릭 시 모달로 띄우기
<Link onClick={() => setPrivacyModalOpen(true)}>
  개인정보처리방침
</Link>

<Dialog maxWidth="md" fullWidth>
  <DialogTitle>개인정보처리방침</DialogTitle>
  <DialogContent>
    <PrivacyPolicyContent />
  </DialogContent>
</Dialog>
2. 별도 페이지 + 뒒로가기 (SEO 중요할 때)
jsx// /privacy-policy 라우트 생성
// 법적 요구사항이나 SEO가 중요한 경우
3. Accordion 방식 (UX 친화적)실용적인 개인정보처리방침 UI대화형 아티팩트 현업에서 가장 많이 쓰는 방식들:
1. 모달 방식 (80% 채택)

장점: 플로우 중단 없음, 빠른 확인 가능
사용 사례: 회원가입, 로그인, 결제 시
구현: Material-UI Dialog + maxWidth="md"

2. Accordion 방식 (15% 채택)

장점: 필요한 부분만 펼쳐봄, UX 친화적
사용 사례: 설정 페이지, 고객센터
구현: Material-UI Accordion

3. 별도 페이지 (5% 채택)

장점: SEO, 법적 독립성
사용 사례: 푸터 링크, 공식 문서

실무 고려사항:
jsx// 1. 반응형 모달 (필수)
<Dialog
  fullScreen={isMobile}  // 모바일에서는 전체화면
  maxWidth="md"
  scroll="paper"  // 스크롤 최적화
/>

// 2. 스크롤 위치 기억 (긴 텍스트용)
const [scrollPosition, setScrollPosition] = useState(0);

// 3. 로딩 상태 (큰 텍스트용)
const [loading, setLoading] = useState(true);
추천 방식:
회원가입/로그인: 모달
설정 페이지: Accordion
공식 문서: 별도 페이지

<br />

# 🔗 References
- [관련 링크](URL)
