> 📅 Date: 2025-08-07 (Update: 2025-11-06)

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

## Legal Text 관리 전략

### 1. 파일 구조 설계
대부분의 실무 프로젝트에서는 다음과 같은 구조를 선호합니다:

```
src/
├── constants/
│   ├── legalTexts/
│   │   ├── index.js          // 통합 export
│   │   ├── privacyPolicy.js  // 개인정보처리방침
│   │   ├── termsOfService.js // 이용약관
│   │   └── cookiePolicy.js   // 쿠키정책
│   └── textConfig.js         // 텍스트 관련 설정
├── components/
│   └── legal/
│       ├── PolicyModal.jsx
│       ├── PolicyAccordion.jsx
│       └── PolicyContent.jsx
└── hooks/
    └── useLegalText.js       // 텍스트 로딩 로직
```

### 2. 텍스트 데이터 구조화
```javascript
// constants/legalTexts/privacyPolicy.js
export const PRIVACY_POLICY = {
  meta: {
    title: '개인정보처리방침',
    lastUpdated: '2025-11-05',
    version: '2.1',
    effectiveDate: '2025-01-01'
  },
  sections: [
    {
      id: 'collection',
      title: '1. 수집하는 개인정보 항목',
      content: `
        회사는 서비스 제공을 위해 다음과 같은 개인정보를 수집합니다:
        
        가. 필수 정보
        - 이메일 주소, 비밀번호
        - 닉네임, 프로필 사진
        
        나. 선택 정보
        - 전화번호, 주소
        - 관심사, 선호도
      `,
      subsections: [
        {
          title: '가. 회원가입 시',
          items: ['이메일', '비밀번호', '닉네임']
        },
        {
          title: '나. 서비스 이용 시',
          items: ['IP주소', '쿠키', '방문기록']
        }
      ]
    },
    {
      id: 'purpose',
      title: '2. 개인정보 수집 및 이용 목적',
      content: `
        수집된 개인정보는 다음 목적으로만 이용됩니다:
        - 서비스 제공 및 운영
        - 회원 관리 및 고객 상담
        - 마케팅 및 광고 활용 (선택사항)
      `
    }
    // ... 추가 섹션들
  ]
};

// constants/legalTexts/index.js
export { PRIVACY_POLICY } from './privacyPolicy';
export { TERMS_OF_SERVICE } from './termsOfService';
export { COOKIE_POLICY } from './cookiePolicy';

export const LEGAL_TEXT_CONFIG = {
  types: {
    PRIVACY: 'privacy',
    TERMS: 'terms', 
    COOKIES: 'cookies'
  },
  displayModes: {
    MODAL: 'modal',
    PAGE: 'page',
    ACCORDION: 'accordion'
  }
};
```

### 3. 환경별 텍스트 관리
```javascript
// constants/textConfig.js
const isDevelopment = process.env.NODE_ENV === 'development';

export const TEXT_CONFIG = {
  // 개발환경에서는 짧은 더미 텍스트 사용
  useShortText: isDevelopment,
  
  // 외부 API 사용 여부
  useExternalApi: process.env.REACT_APP_USE_CMS === 'true',
  
  // 텍스트 버전 관리
  version: process.env.REACT_APP_LEGAL_VERSION || '1.0',
  
  // 다국어 지원
  defaultLocale: 'ko',
  supportedLocales: ['ko', 'en']
};

// 환경별 텍스트 로딩
export const getLegalText = (type, locale = 'ko') => {
  if (TEXT_CONFIG.useExternalApi) {
    return fetchFromCMS(type, locale);
  }
  
  if (TEXT_CONFIG.useShortText) {
    return DUMMY_TEXTS[type];
  }
  
  return LEGAL_TEXTS[type];
};
```

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

## UI 패턴 구현

### 1. 모달 방식 (가장 일반적 - 80% 채택)

```jsx
// components/legal/PolicyModal.jsx
import React, { useState, useEffect } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  Typography,
  Box,
  IconButton,
  useMediaQuery,
  useTheme
} from '@mui/material';
import { Close as CloseIcon } from '@mui/icons-material';

const PolicyModal = ({ 
  open, 
  onClose, 
  policyType = 'privacy',
  title,
  content 
}) => {
  const theme = useTheme();
  const isMobile = useMediaQuery(theme.breakpoints.down('md'));
  const [scrollPosition, setScrollPosition] = useState(0);

  // 스크롤 위치 기억
  const handleScroll = (e) => {
    setScrollPosition(e.target.scrollTop);
  };

  // 모달 닫을 때 스크롤 위치 초기화
  useEffect(() => {
    if (!open) {
      setScrollPosition(0);
    }
  }, [open]);

  return (
    <Dialog
      open={open}
      onClose={onClose}
      maxWidth="md"
      fullWidth
      fullScreen={isMobile} // 모바일에서는 전체화면
      scroll="paper"
      PaperProps={{
        sx: {
          height: isMobile ? '100vh' : '80vh',
          maxHeight: isMobile ? '100vh' : '80vh'
        }
      }}
    >
      <DialogTitle sx={{ 
        display: 'flex', 
        justifyContent: 'space-between', 
        alignItems: 'center',
        borderBottom: 1,
        borderColor: 'divider'
      }}>
        <Typography variant="h6" component="div">
          {title}
        </Typography>
        <IconButton onClick={onClose} size="small">
          <CloseIcon />
        </IconButton>
      </DialogTitle>

      <DialogContent 
        dividers
        onScroll={handleScroll}
        sx={{ 
          padding: { xs: 2, md: 3 },
          '&::-webkit-scrollbar': {
            width: '8px',
          },
          '&::-webkit-scrollbar-track': {
            background: '#f1f1f1',
          },
          '&::-webkit-scrollbar-thumb': {
            background: '#c1c1c1',
            borderRadius: '4px',
          }
        }}
      >
        <PolicyContent content={content} />
      </DialogContent>

      <DialogActions sx={{ padding: 2 }}>
        <Button 
          onClick={onClose} 
          variant="contained" 
          fullWidth={isMobile}
        >
          확인
        </Button>
      </DialogActions>
    </Dialog>
  );
};

// 정책 내용 렌더링 컴포넌트
const PolicyContent = ({ content }) => {
  if (typeof content === 'string') {
    return (
      <Typography variant="body2" sx={{ lineHeight: 1.8, whiteSpace: 'pre-line' }}>
        {content}
      </Typography>
    );
  }

  // 구조화된 데이터인 경우
  return (
    <Box>
      {content.sections?.map((section, index) => (
        <Box key={section.id || index} sx={{ mb: 3 }}>
          <Typography variant="h6" sx={{ mb: 2, fontWeight: 600 }}>
            {section.title}
          </Typography>
          <Typography variant="body2" sx={{ mb: 2, lineHeight: 1.8 }}>
            {section.content}
          </Typography>
          
          {section.subsections && (
            <Box sx={{ ml: 2 }}>
              {section.subsections.map((sub, subIndex) => (
                <Box key={subIndex} sx={{ mb: 1 }}>
                  <Typography variant="subtitle2" sx={{ fontWeight: 500 }}>
                    {sub.title}
                  </Typography>
                  <ul style={{ margin: '8px 0', paddingLeft: '20px' }}>
                    {sub.items.map((item, itemIndex) => (
                      <li key={itemIndex}>
                        <Typography variant="body2">{item}</Typography>
                      </li>
                    ))}
                  </ul>
                </Box>
              ))}
            </Box>
          )}
        </Box>
      ))}
    </Box>
  );
};

export default PolicyModal;
```

### 2. Accordion 방식 (UX 친화적 - 15% 채택)

```jsx
// components/legal/PolicyAccordion.jsx
import React, { useState } from 'react';
import {
  Accordion,
  AccordionSummary,
  AccordionDetails,
  Typography,
  Box,
  Chip
} from '@mui/material';
import ExpandMoreIcon from '@mui/icons-material/ExpandMore';

const PolicyAccordion = ({ policyData, defaultExpanded = false }) => {
  const [expanded, setExpanded] = useState(defaultExpanded ? 0 : false);

  const handleChange = (panel) => (event, isExpanded) => {
    setExpanded(isExpanded ? panel : false);
  };

  return (
    <Box sx={{ width: '100%' }}>
      <Box sx={{ mb: 2, display: 'flex', alignItems: 'center', gap: 1 }}>
        <Typography variant="h6">{policyData.meta.title}</Typography>
        <Chip 
          label={`v${policyData.meta.version}`} 
          size="small" 
          color="primary" 
          variant="outlined"
        />
        <Chip 
          label={`업데이트: ${policyData.meta.lastUpdated}`} 
          size="small" 
          variant="outlined"
        />
      </Box>

      {policyData.sections.map((section, index) => (
        <Accordion
          key={section.id}
          expanded={expanded === index}
          onChange={handleChange(index)}
          sx={{
            '&:before': { display: 'none' },
            boxShadow: 1,
            '&.Mui-expanded': {
              margin: '8px 0',
            }
          }}
        >
          <AccordionSummary
            expandIcon={<ExpandMoreIcon />}
            sx={{
              backgroundColor: 'rgba(0, 0, 0, .03)',
              '&.Mui-expanded': {
                backgroundColor: 'primary.light',
                color: 'primary.contrastText'
              }
            }}
          >
            <Typography variant="subtitle1" sx={{ fontWeight: 500 }}>
              {section.title}
            </Typography>
          </AccordionSummary>
          
          <AccordionDetails sx={{ padding: 3 }}>
            <Typography variant="body2" sx={{ lineHeight: 1.8, mb: 2 }}>
              {section.content}
            </Typography>
            
            {section.subsections && (
              <Box sx={{ mt: 2 }}>
                {section.subsections.map((sub, subIndex) => (
                  <Box key={subIndex} sx={{ mb: 2, ml: 1 }}>
                    <Typography variant="subtitle2" sx={{ fontWeight: 500, mb: 1 }}>
                      {sub.title}
                    </Typography>
                    <Box component="ul" sx={{ m: 0, pl: 2 }}>
                      {sub.items.map((item, itemIndex) => (
                        <Typography component="li" variant="body2" key={itemIndex}>
                          {item}
                        </Typography>
                      ))}
                    </Box>
                  </Box>
                ))}
              </Box>
            )}
          </AccordionDetails>
        </Accordion>
      ))}
    </Box>
  );
};

export default PolicyAccordion;
```

### 3. 별도 페이지 방식 (SEO 중요할 때 - 5% 채택)

```jsx
// pages/PolicyPage.jsx
import React, { useEffect, useState } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import {
  Container,
  Paper,
  Typography,
  Box,
  Button,
  Breadcrumbs,
  Link,
  Skeleton
} from '@mui/material';
import { ArrowBack, Print, Share } from '@mui/icons-material';

const PolicyPage = () => {
  const { policyType } = useParams(); // 'privacy', 'terms', 'cookies'
  const navigate = useNavigate();
  const [policyData, setPolicyData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadPolicy = async () => {
      try {
        // 동적 import로 필요한 정책만 로딩
        const { default: policy } = await import(`../constants/legalTexts/${policyType}Policy.js`);
        setPolicyData(policy);
      } catch (error) {
        console.error('정책 로딩 실패:', error);
      } finally {
        setLoading(false);
      }
    };

    loadPolicy();
  }, [policyType]);

  const handlePrint = () => {
    window.print();
  };

  const handleShare = async () => {
    if (navigator.share) {
      await navigator.share({
        title: policyData?.meta.title,
        url: window.location.href
      });
    } else {
      navigator.clipboard.writeText(window.location.href);
      // 토스트 메시지 표시
    }
  };

  if (loading) {
    return (
      <Container maxWidth="md" sx={{ py: 4 }}>
        <Skeleton variant="text" width="60%" height={40} />
        <Skeleton variant="text" width="30%" height={20} sx={{ mt: 1 }} />
        <Box sx={{ mt: 3 }}>
          {[...Array(5)].map((_, i) => (
            <Skeleton key={i} variant="text" height={60} sx={{ mt: 2 }} />
          ))}
        </Box>
      </Container>
    );
  }

  if (!policyData) {
    return (
      <Container maxWidth="md" sx={{ py: 4, textAlign: 'center' }}>
        <Typography variant="h5">정책을 찾을 수 없습니다</Typography>
        <Button onClick={() => navigate(-1)} sx={{ mt: 2 }}>
          돌아가기
        </Button>
      </Container>
    );
  }

  return (
    <Container maxWidth="md" sx={{ py: 4 }}>
      {/* 브레드크럼 */}
      <Breadcrumbs sx={{ mb: 2 }}>
        <Link color="inherit" href="/" underline="hover">
          홈
        </Link>
        <Link color="inherit" href="/legal" underline="hover">
          법적 고지
        </Link>
        <Typography color="text.primary">
          {policyData.meta.title}
        </Typography>
      </Breadcrumbs>

      <Paper elevation={1} sx={{ p: { xs: 2, md: 4 } }}>
        {/* 헤더 */}
        <Box sx={{ display: 'flex', alignItems: 'center', mb: 3 }}>
          <Button
            startIcon={<ArrowBack />}
            onClick={() => navigate(-1)}
            sx={{ mr: 2 }}
          >
            뒤로
          </Button>
          <Box sx={{ flexGrow: 1 }}>
            <Typography variant="h4" component="h1" sx={{ mb: 1 }}>
              {policyData.meta.title}
            </Typography>
            <Typography variant="body2" color="text.secondary">
              최종 업데이트: {policyData.meta.lastUpdated} | 
              버전: {policyData.meta.version}
            </Typography>
          </Box>
          <Box sx={{ display: 'flex', gap: 1 }}>
            <Button size="small" startIcon={<Print />} onClick={handlePrint}>
              인쇄
            </Button>
            <Button size="small" startIcon={<Share />} onClick={handleShare}>
              공유
            </Button>
          </Box>
        </Box>

        {/* 내용 */}
        <PolicyContent content={policyData} />

        {/* 푸터 */}
        <Box sx={{ mt: 4, pt: 3, borderTop: 1, borderColor: 'divider' }}>
          <Typography variant="body2" color="text.secondary">
            이 정책은 {policyData.meta.effectiveDate}부터 시행됩니다.
          </Typography>
        </Box>
      </Paper>
    </Container>
  );
};

export default PolicyPage;
```

## 고급 기능 구현

### 1. 텍스트 로딩 최적화 Hook

```javascript
// hooks/useLegalText.js
import { useState, useEffect, useCallback } from 'react';
import { getLegalText } from '../constants/textConfig';

export const useLegalText = (textType, options = {}) => {
  const [text, setText] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const { locale = 'ko', useCache = true } = options;

  // 캐시 관리
  const cacheKey = `legal_text_${textType}_${locale}`;
  
  const loadText = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);

      // 캐시 확인
      if (useCache) {
        const cached = sessionStorage.getItem(cacheKey);
        if (cached) {
          setText(JSON.parse(cached));
          setLoading(false);
          return;
        }
      }

      // 텍스트 로딩
      const textData = await getLegalText(textType, locale);
      setText(textData);
      
      // 캐시 저장
      if (useCache) {
        sessionStorage.setItem(cacheKey, JSON.stringify(textData));
      }
    } catch (err) {
      setError(err);
      console.error(`Failed to load ${textType} text:`, err);
    } finally {
      setLoading(false);
    }
  }, [textType, locale, useCache, cacheKey]);

  useEffect(() => {
    loadText();
  }, [loadText]);

  const refresh = useCallback(() => {
    // 캐시 삭제하고 다시 로딩
    sessionStorage.removeItem(cacheKey);
    loadText();
  }, [cacheKey, loadText]);

  return {
    text,
    loading,
    error,
    refresh
  };
};

// 사용 예시
const PrivacyPolicyModal = ({ open, onClose }) => {
  const { text, loading, error } = useLegalText('privacy', { 
    locale: 'ko',
    useCache: true 
  });

  if (error) return <div>텍스트 로딩 실패</div>;
  
  return (
    <PolicyModal
      open={open}
      onClose={onClose}
      title="개인정보처리방침"
      content={text}
      loading={loading}
    />
  );
};
```

### 2. 반응형 및 접근성 고려사항

```scss
// styles/legal.scss
.legal-text {
  // 기본 스타일
  line-height: 1.8;
  word-break: keep-all;
  
  // 반응형 폰트 크기
  font-size: clamp(14px, 2vw, 16px);
  
  // 제목 스타일
  h1, h2, h3, h4, h5, h6 {
    margin-top: 2em;
    margin-bottom: 0.8em;
    font-weight: 600;
  }
  
  // 목록 스타일
  ul, ol {
    padding-left: 1.5em;
    margin-bottom: 1em;
    
    li {
      margin-bottom: 0.5em;
    }
  }
  
  // 인용구 스타일
  blockquote {
    border-left: 4px solid #ddd;
    margin: 1em 0;
    padding-left: 1em;
    color: #666;
  }
  
  // 모바일 최적화
  @media (max-width: 768px) {
    font-size: 14px;
    
    h1 { font-size: 1.5em; }
    h2 { font-size: 1.3em; }
    h3 { font-size: 1.1em; }
    
    ul, ol {
      padding-left: 1.2em;
    }
  }
  
  // 인쇄 스타일
  @media print {
    font-size: 12pt;
    line-height: 1.6;
    color: black;
    
    // 페이지 브레이크 제어
    h1, h2, h3 {
      page-break-after: avoid;
    }
    
    // 링크 URL 표시
    a[href]:after {
      content: " (" attr(href) ")";
      font-size: 0.8em;
    }
  }
}

// 접근성 개선
.legal-modal {
  // 포커스 트랩
  &:focus-within {
    outline: none;
  }
  
  // 스크린 리더용 건너뛰기 링크
  .skip-link {
    position: absolute;
    top: -40px;
    left: 6px;
    background: #000;
    color: #fff;
    padding: 8px;
    text-decoration: none;
    z-index: 1000;
    
    &:focus {
      top: 6px;
    }
  }
}
```

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

## 성능 최적화 및 모니터링

### 1. Bundle 크기 최적화

```javascript
// webpack.config.js - 코드 스플리팅 설정
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        // 법적 텍스트 별도 청크로 분리
        legalTexts: {
          test: /[\\/]constants[\\/]legalTexts[\\/]/,
          name: 'legal-texts',
          chunks: 'all',
          priority: 10,
        },
      },
    },
  },
};

// Dynamic import로 지연 로딩
const loadLegalText = async (textType) => {
  const { default: text } = await import(
    `../constants/legalTexts/${textType}Policy.js`
    /* webpackChunkName: "legal-[request]" */
  );
  return text;
};
```

### 2. 캐싱 전략

```javascript
// utils/legalTextCache.js
class LegalTextCache {
  constructor() {
    this.memoryCache = new Map();
    this.cacheTimeout = 30 * 60 * 1000; // 30분
  }

  // 메모리 캐시 설정
  set(key, data) {
    this.memoryCache.set(key, {
      data,
      timestamp: Date.now(),
    });
  }

  // 메모리 캐시 조회
  get(key) {
    const cached = this.memoryCache.get(key);
    if (!cached) return null;

    // 캐시 만료 확인
    if (Date.now() - cached.timestamp > this.cacheTimeout) {
      this.memoryCache.delete(key);
      return null;
    }

    return cached.data;
  }

  // 브라우저 스토리지 캐시
  setSessionCache(key, data) {
    try {
      sessionStorage.setItem(key, JSON.stringify({
        data,
        timestamp: Date.now()
      }));
    } catch (error) {
      console.warn('Session storage full:', error);
    }
  }

  getSessionCache(key) {
    try {
      const cached = sessionStorage.getItem(key);
      if (!cached) return null;

      const { data, timestamp } = JSON.parse(cached);
      
      if (Date.now() - timestamp > this.cacheTimeout) {
        sessionStorage.removeItem(key);
        return null;
      }

      return data;
    } catch (error) {
      console.warn('Session cache error:', error);
      return null;
    }
  }

  // 캐시 크기 관리
  clearExpiredCache() {
    const now = Date.now();
    for (const [key, value] of this.memoryCache.entries()) {
      if (now - value.timestamp > this.cacheTimeout) {
        this.memoryCache.delete(key);
      }
    }
  }
}

export const legalTextCache = new LegalTextCache();

// 주기적 캐시 정리
setInterval(() => {
  legalTextCache.clearExpiredCache();
}, 5 * 60 * 1000); // 5분마다
```

### 3. 성능 측정 및 모니터링

```javascript
// utils/performanceMonitor.js
export class LegalTextPerformanceMonitor {
  constructor() {
    this.metrics = new Map();
  }

  // 로딩 시간 측정
  startTiming(textType) {
    const key = `legal_text_${textType}`;
    this.metrics.set(key, {
      startTime: performance.now(),
      textType
    });
  }

  endTiming(textType, success = true) {
    const key = `legal_text_${textType}`;
    const metric = this.metrics.get(key);
    
    if (!metric) return;

    const duration = performance.now() - metric.startTime;
    
    // 성능 로그
    console.log(`Legal text ${textType} loaded in ${duration.toFixed(2)}ms`);
    
    // 분석 도구로 전송 (예: Google Analytics)
    if (window.gtag) {
      window.gtag('event', 'legal_text_load', {
        event_category: 'performance',
        event_label: textType,
        value: Math.round(duration),
        custom_parameter_1: success ? 'success' : 'error'
      });
    }

    this.metrics.delete(key);
  }

  // 번들 크기 분석
  analyzeBundleSize() {
    if (process.env.NODE_ENV === 'development') {
      import('webpack-bundle-analyzer')
        .then(({ BundleAnalyzerPlugin }) => {
          console.log('Bundle analysis available');
        })
        .catch(() => {
          console.log('Bundle analyzer not available');
        });
    }
  }
}

export const performanceMonitor = new LegalTextPerformanceMonitor();
```

## 테스팅 전략

### 1. 단위 테스트 (Jest + Testing Library)

```javascript
// __tests__/PolicyModal.test.jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ThemeProvider } from '@mui/material/styles';
import { createTheme } from '@mui/material/styles';
import PolicyModal from '../components/legal/PolicyModal';

const theme = createTheme();

const mockPolicyData = {
  meta: {
    title: 'Test Policy',
    version: '1.0',
    lastUpdated: '2024-01-01'
  },
  sections: [
    {
      id: 'section1',
      title: 'Test Section',
      content: 'Test content'
    }
  ]
};

const renderWithTheme = (component) => {
  return render(
    <ThemeProvider theme={theme}>
      {component}
    </ThemeProvider>
  );
};

describe('PolicyModal', () => {
  it('renders policy modal correctly', () => {
    renderWithTheme(
      <PolicyModal
        open={true}
        onClose={jest.fn()}
        title="Test Policy"
        content={mockPolicyData}
      />
    );

    expect(screen.getByText('Test Policy')).toBeInTheDocument();
    expect(screen.getByText('Test Section')).toBeInTheDocument();
    expect(screen.getByText('Test content')).toBeInTheDocument();
  });

  it('closes modal when close button is clicked', async () => {
    const onClose = jest.fn();
    renderWithTheme(
      <PolicyModal
        open={true}
        onClose={onClose}
        title="Test Policy"
        content={mockPolicyData}
      />
    );

    const closeButton = screen.getByRole('button', { name: /close/i });
    await userEvent.click(closeButton);

    expect(onClose).toHaveBeenCalledTimes(1);
  });

  it('handles mobile responsive behavior', () => {
    // 모바일 화면 크기 시뮬레이션
    Object.defineProperty(window, 'innerWidth', {
      writable: true,
      configurable: true,
      value: 375,
    });

    renderWithTheme(
      <PolicyModal
        open={true}
        onClose={jest.fn()}
        title="Test Policy"
        content={mockPolicyData}
      />
    );

    // 모바일에서 전체화면 모달 확인
    const dialog = screen.getByRole('dialog');
    expect(dialog).toHaveClass('MuiDialog-paperFullScreen');
  });

  it('handles scroll position correctly', async () => {
    renderWithTheme(
      <PolicyModal
        open={true}
        onClose={jest.fn()}
        title="Test Policy"
        content={mockPolicyData}
      />
    );

    const content = screen.getByTestId('dialog-content');
    
    // 스크롤 이벤트 시뮬레이션
    fireEvent.scroll(content, { target: { scrollTop: 100 } });
    
    expect(content.scrollTop).toBe(100);
  });
});
```

### 2. 접근성 테스트 (axe-core)

```javascript
// __tests__/accessibility.test.jsx
import React from 'react';
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import PolicyModal from '../components/legal/PolicyModal';

expect.extend(toHaveNoViolations);

describe('Legal Components Accessibility', () => {
  it('PolicyModal should not have accessibility violations', async () => {
    const { container } = render(
      <PolicyModal
        open={true}
        onClose={jest.fn()}
        title="Test Policy"
        content="Test content"
      />
    );

    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  it('should have proper ARIA labels', () => {
    render(
      <PolicyModal
        open={true}
        onClose={jest.fn()}
        title="Privacy Policy"
        content="Privacy content"
      />
    );

    expect(screen.getByRole('dialog')).toHaveAttribute('aria-labelledby');
    expect(screen.getByRole('button', { name: /close/i })).toHaveAttribute('aria-label');
  });

  it('should handle keyboard navigation', async () => {
    const onClose = jest.fn();
    render(
      <PolicyModal
        open={true}
        onClose={onClose}
        title="Test Policy"
        content="Test content"
      />
    );

    // ESC 키로 모달 닫기
    fireEvent.keyDown(document, { key: 'Escape', code: 'Escape' });
    expect(onClose).toHaveBeenCalled();
  });
});
```

### 3. E2E 테스트 (Playwright)

```javascript
// e2e/legal-modal.spec.js
import { test, expect } from '@playwright/test';

test.describe('Legal Text Modal', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/');
  });

  test('should open and close privacy policy modal', async ({ page }) => {
    // 개인정보처리방침 링크 클릭
    await page.click('text=개인정보처리방침');
    
    // 모달이 열렸는지 확인
    await expect(page.locator('[role="dialog"]')).toBeVisible();
    await expect(page.locator('text=개인정보처리방침')).toBeVisible();
    
    // 모달 닫기
    await page.click('[aria-label="close"]');
    await expect(page.locator('[role="dialog"]')).not.toBeVisible();
  });

  test('should scroll through long policy content', async ({ page }) => {
    await page.click('text=이용약관');
    
    const modal = page.locator('[role="dialog"]');
    await expect(modal).toBeVisible();
    
    // 스크롤 테스트
    const content = modal.locator('.MuiDialogContent-root');
    await content.scroll({ top: 500 });
    
    // 스크롤 위치 확인
    const scrollTop = await content.evaluate(el => el.scrollTop);
    expect(scrollTop).toBeGreaterThan(400);
  });

  test('should be responsive on mobile', async ({ page, isMobile }) => {
    if (isMobile) {
      await page.click('text=개인정보처리방침');
      
      const modal = page.locator('[role="dialog"]');
      await expect(modal).toBeVisible();
      
      // 모바일에서 전체화면인지 확인
      const modalBox = await modal.boundingBox();
      const viewport = page.viewportSize();
      
      expect(modalBox.width).toBe(viewport.width);
      expect(modalBox.height).toBe(viewport.height);
    }
  });
});
```

## 유지보수 및 업데이트 전략

### 1. 버전 관리 시스템

```javascript
// utils/legalTextVersioning.js
export class LegalTextVersionManager {
  constructor() {
    this.currentVersions = new Map();
    this.updateCallbacks = new Set();
  }

  // 버전 확인
  async checkForUpdates() {
    try {
      const response = await fetch('/api/legal-texts/versions');
      const serverVersions = await response.json();
      
      for (const [textType, serverVersion] of Object.entries(serverVersions)) {
        const currentVersion = this.currentVersions.get(textType);
        
        if (!currentVersion || currentVersion !== serverVersion) {
          await this.updateText(textType, serverVersion);
        }
      }
    } catch (error) {
      console.error('Failed to check for legal text updates:', error);
    }
  }

  // 텍스트 업데이트
  async updateText(textType, newVersion) {
    try {
      // 캐시 삭제
      legalTextCache.clearCache(textType);
      
      // 새 버전 저장
      this.currentVersions.set(textType, newVersion);
      
      // 업데이트 콜백 실행
      for (const callback of this.updateCallbacks) {
        callback(textType, newVersion);
      }

      console.log(`Legal text ${textType} updated to version ${newVersion}`);
    } catch (error) {
      console.error(`Failed to update ${textType}:`, error);
    }
  }

  // 업데이트 콜백 등록
  onUpdate(callback) {
    this.updateCallbacks.add(callback);
    
    return () => {
      this.updateCallbacks.delete(callback);
    };
  }
}

export const versionManager = new LegalTextVersionManager();

// 주기적 업데이트 확인 (1시간마다)
setInterval(() => {
  versionManager.checkForUpdates();
}, 60 * 60 * 1000);
```

### 2. 업데이트 알림 시스템

```javascript
// components/legal/UpdateNotification.jsx
import React, { useState, useEffect } from 'react';
import {
  Snackbar,
  Alert,
  Button,
  Box
} from '@mui/material';
import { versionManager } from '../../utils/legalTextVersioning';

const LegalTextUpdateNotification = () => {
  const [updates, setUpdates] = useState([]);
  const [open, setOpen] = useState(false);

  useEffect(() => {
    const unsubscribe = versionManager.onUpdate((textType, version) => {
      setUpdates(prev => [...prev, { textType, version, timestamp: Date.now() }]);
      setOpen(true);
    });

    return unsubscribe;
  }, []);

  const handleClose = () => {
    setOpen(false);
    setTimeout(() => setUpdates([]), 300);
  };

  const handleViewChanges = () => {
    // 변경사항 모달 열기
    setOpen(false);
  };

  if (updates.length === 0) return null;

  return (
    <Snackbar
      open={open}
      autoHideDuration={10000}
      onClose={handleClose}
      anchorOrigin={{ vertical: 'top', horizontal: 'center' }}
    >
      <Alert
        severity="info"
        action={
          <Box sx={{ display: 'flex', gap: 1 }}>
            <Button 
              color="inherit" 
              size="small" 
              onClick={handleViewChanges}
            >
              변경사항 보기
            </Button>
            <Button 
              color="inherit" 
              size="small" 
              onClick={handleClose}
            >
              닫기
            </Button>
          </Box>
        }
      >
        {updates.length === 1 
          ? `${updates[0].textType} 정책이 업데이트되었습니다.`
          : `${updates.length}개의 정책이 업데이트되었습니다.`
        }
      </Alert>
    </Snackbar>
  );
};

export default LegalTextUpdateNotification;
```

## 실제 프로젝트 적용 사례

### 1. 대규모 서비스 (네이버, 카카오급)

```javascript
// 예시: 카카오톡 스타일 정책 관리
const KAKAO_LEGAL_CONFIG = {
  textSources: {
    privacy: {
      ko: () => import('../legal/ko/privacy_v2.3.js'),
      en: () => import('../legal/en/privacy_v2.3.js'),
      ja: () => import('../legal/ja/privacy_v2.3.js')
    },
    terms: {
      ko: () => import('../legal/ko/terms_v1.8.js'),
      en: () => import('../legal/en/terms_v1.8.js')
    }
  },
  
  // 지역별 법적 요구사항
  regionalRequirements: {
    EU: ['privacy', 'cookies', 'gdpr'],
    US: ['privacy', 'terms', 'ccpa'],
    KR: ['privacy', 'terms', 'pipa']
  },
  
  // A/B 테스트 대상 텍스트
  experiments: {
    'privacy-modal-variant': {
      control: 'modal',
      variant: 'accordion',
      trafficSplit: 0.1
    }
  }
};
```

### 2. 스타트업 단계별 적용

```javascript
// MVP 단계: 최소 구현
const MVP_LEGAL_TEXTS = {
  privacy: "간단한 개인정보처리방침 텍스트...",
  terms: "기본 이용약관 텍스트..."
};

// 성장 단계: 구조화된 관리
const GROWTH_LEGAL_CONFIG = {
  texts: {
    privacy: {
      sections: [...],
      lastUpdated: '2024-01-01',
      version: '1.0'
    }
  },
  ui: {
    displayType: 'modal',
    responsive: true
  }
};

// 확장 단계: 완전한 시스템
const ENTERPRISE_LEGAL_SYSTEM = {
  // 위의 모든 기능 포함
  multiLanguage: true,
  versionControl: true,
  analytics: true,
  accessibility: true,
  performance: true
};
```

<br />

# 🔗 References
- [관련 링크](URL)
