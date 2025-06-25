> 📅 Date: 2025-06-25

# 📌 Focus
- JavaScript Intl API
- 국제화(Internationalization, i18n)
- 지역화(Localization)
- 다국어 웹 애플리케이션 개발
<br />

# 📝 Learnings

## Intl API란?

JavaScript의 **Intl API**는 internationalization tasks, such as formatting dates and numbers, sorting and comparing strings, and translating text를 처리하기 위한 브라우저 내장 API다. 별도의 라이브러리 설치 없이 all modern web browsers, including Google Chrome, Mozilla Firefox, Apple Safari, and Microsoft Edge에서 사용할 수 있다.

국제화(i18n)는 단순히 텍스트 번역만이 아니라 displaying currencies, numbers, dates, and times in the format of the customer's locale을 포함한 문화적 관습에 맞는 소프트웨어를 만드는 것이다.

### 왜 Intl API를 사용해야 할까?

기존에는 moment.js, date-fns, numeral.js 같은 라이브러리를 사용했지만, Intl API를 사용하면:
- **번들 크기 절약**: 외부 라이브러리 불필요
- **브라우저 표준**: 모든 모던 브라우저에서 지원
- **성능 최적화**: 브라우저 엔진 레벨에서 최적화
- **정확성**: 각 지역의 문화적 관습을 정확히 반영

## 주요 Intl 객체들

### 1. Intl.DateTimeFormat - 날짜/시간 포맷팅

```javascript
// 기본 사용법
const date = new Date('2024-03-15T14:30:00');

// 한국어 형식
const koFormatter = new Intl.DateTimeFormat('ko-KR');
console.log(koFormatter.format(date)); // "2024. 3. 15."

// 영어 형식
const enFormatter = new Intl.DateTimeFormat('en-US');
console.log(enFormatter.format(date)); // "3/15/2024"

// 독일어 형식
const deFormatter = new Intl.DateTimeFormat('de-DE');
console.log(deFormatter.format(date)); // "15.3.2024"

// 상세 옵션 설정
const detailedFormatter = new Intl.DateTimeFormat('ko-KR', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  weekday: 'long',
  hour: '2-digit',
  minute: '2-digit',
  hour12: false
});
console.log(detailedFormatter.format(date)); 
// "2024년 3월 15일 금요일 14:30"

// 실용적인 React 예시
const DateDisplay = ({ date, locale = 'ko-KR' }) => {
  const formatter = new Intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  });
  
  return <span>{formatter.format(new Date(date))}</span>;
};
```

### 2. Intl.NumberFormat - 숫자/통화 포맷팅

```javascript
const number = 1234567.89;

// 기본 숫자 포맷팅
console.log(new Intl.NumberFormat('ko-KR').format(number));
// "1,234,567.89"

console.log(new Intl.NumberFormat('de-DE').format(number));
// "1.234.567,89" (독일은 소수점이 쉼표)

// 통화 포맷팅
const currency = 1234567;

// 원화
const krwFormatter = new Intl.NumberFormat('ko-KR', {
  style: 'currency',
  currency: 'KRW'
});
console.log(krwFormatter.format(currency)); // "₩1,234,567"

// 달러
const usdFormatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD'
});
console.log(usdFormatter.format(currency)); // "$1,234,567.00"

// 유로
const eurFormatter = new Intl.NumberFormat('de-DE', {
  style: 'currency',
  currency: 'EUR'
});
console.log(eurFormatter.format(currency)); // "1.234.567,00 €"

// 퍼센트 포맷팅
const percentage = 0.75;
const percentFormatter = new Intl.NumberFormat('ko-KR', {
  style: 'percent',
  minimumFractionDigits: 1
});
console.log(percentFormatter.format(percentage)); // "75.0%"

// 컴팩트 표기법 (1K, 1M 등)
const compactFormatter = new Intl.NumberFormat('en-US', {
  notation: 'compact',
  compactDisplay: 'short'
});
console.log(compactFormatter.format(1200000)); // "1.2M"

// 실용적인 React 컴포넌트
const PriceDisplay = ({ amount, currency = 'KRW', locale = 'ko-KR' }) => {
  const formatter = new Intl.NumberFormat(locale, {
    style: 'currency',
    currency: currency,
    minimumFractionDigits: 0
  });
  
  return <span className="price">{formatter.format(amount)}</span>;
};
```

### 3. Intl.RelativeTimeFormat - 상대적 시간 표현

```javascript
// 상대적 시간 포맷터 생성
const rtf = new Intl.RelativeTimeFormat('ko-KR', { 
  numeric: 'auto' 
});

// 과거 시간
console.log(rtf.format(-1, 'day'));    // "어제"
console.log(rtf.format(-2, 'day'));    // "2일 전"
console.log(rtf.format(-1, 'week'));   // "지난주"
console.log(rtf.format(-1, 'month'));  // "지난달"

// 미래 시간
console.log(rtf.format(1, 'day'));     // "내일"
console.log(rtf.format(2, 'hour'));    // "2시간 후"
console.log(rtf.format(1, 'week'));    // "다음 주"

// 영어 버전
const rtfEn = new Intl.RelativeTimeFormat('en-US', { 
  numeric: 'auto' 
});
console.log(rtfEn.format(-1, 'day'));  // "yesterday"
console.log(rtfEn.format(-2, 'day'));  // "2 days ago"

// 실용적인 함수
const formatRelativeTime = (date, locale = 'ko-KR') => {
  const rtf = new Intl.RelativeTimeFormat(locale, { numeric: 'auto' });
  const now = new Date();
  const diffInSeconds = Math.floor((date - now) / 1000);
  
  const intervals = {
    year: 31536000,
    month: 2592000,
    week: 604800,
    day: 86400,
    hour: 3600,
    minute: 60
  };
  
  for (const [unit, seconds] of Object.entries(intervals)) {
    const interval = Math.floor(diffInSeconds / seconds);
    if (Math.abs(interval) >= 1) {
      return rtf.format(interval, unit);
    }
  }
  
  return rtf.format(diffInSeconds, 'second');
};

// 사용 예시
const pastDate = new Date(Date.now() - 2 * 24 * 60 * 60 * 1000); // 2일 전
console.log(formatRelativeTime(pastDate)); // "2일 전"
```

### 4. Intl.ListFormat - 목록 포맷팅

```javascript
const items = ['사과', '바나나', '오렌지'];

// 한국어 목록
const listFormatter = new Intl.ListFormat('ko-KR', {
  style: 'long',
  type: 'conjunction'  // 'and' 형태
});
console.log(listFormatter.format(items)); // "사과, 바나나 및 오렌지"

// 영어 목록
const listFormatterEn = new Intl.ListFormat('en-US', {
  style: 'long',
  type: 'conjunction'
});
console.log(listFormatterEn.format(items)); // "사과, 바나나, and 오렌지"

// 'or' 형태
const orFormatter = new Intl.ListFormat('ko-KR', {
  style: 'long',
  type: 'disjunction'
});
console.log(orFormatter.format(items)); // "사과, 바나나 또는 오렌지"

// 태그 리스트 컴포넌트 예시
const TagList = ({ tags, locale = 'ko-KR' }) => {
  const formatter = new Intl.ListFormat(locale, {
    style: 'short',
    type: 'conjunction'
  });
  
  return (
    <div className="tags">
      태그: {formatter.format(tags)}
    </div>
  );
};
```

### 5. Intl.PluralRules - 복수형 규칙

```javascript
// 한국어는 복수형 구분이 없지만, 영어는 있음
const pr = new Intl.PluralRules('en-US');

console.log(pr.select(0));  // "other"
console.log(pr.select(1));  // "one"
console.log(pr.select(2));  // "other"

// 실용적인 복수형 처리 함수
const pluralize = (count, singular, plural, locale = 'en-US') => {
  const pr = new Intl.PluralRules(locale);
  const rule = pr.select(count);
  
  const forms = {
    one: singular,
    other: plural
  };
  
  return `${count} ${forms[rule] || forms.other}`;
};

console.log(pluralize(1, 'item', 'items')); // "1 item"
console.log(pluralize(5, 'item', 'items')); // "5 items"

// React 컴포넌트 예시
const ItemCounter = ({ count }) => {
  const text = pluralize(count, 'item', 'items');
  return <span>{text}</span>;
};
```

## 고급 활용 예시

### 1. 다국어 지원 커스텀 훅

```typescript
import { useState, useEffect } from 'react';

type Locale = 'ko-KR' | 'en-US' | 'ja-JP' | 'zh-CN';

interface UseIntlOptions {
  locale: Locale;
  fallbackLocale?: Locale;
}

const useIntl = ({ locale, fallbackLocale = 'en-US' }: UseIntlOptions) => {
  const [currentLocale, setCurrentLocale] = useState<Locale>(locale);

  // 날짜 포맷터
  const formatDate = (date: Date, options?: Intl.DateTimeFormatOptions) => {
    try {
      return new Intl.DateTimeFormat(currentLocale, options).format(date);
    } catch (error) {
      return new Intl.DateTimeFormat(fallbackLocale, options).format(date);
    }
  };

  // 숫자 포맷터
  const formatNumber = (number: number, options?: Intl.NumberFormatOptions) => {
    try {
      return new Intl.NumberFormat(currentLocale, options).format(number);
    } catch (error) {
      return new Intl.NumberFormat(fallbackLocale, options).format(number);
    }
  };

  // 통화 포맷터
  const formatCurrency = (amount: number, currency: string) => {
    return formatNumber(amount, {
      style: 'currency',
      currency: currency
    });
  };

  // 상대시간 포맷터
  const formatRelativeTime = (date: Date) => {
    const rtf = new Intl.RelativeTimeFormat(currentLocale, { numeric: 'auto' });
    const now = new Date();
    const diffInMinutes = Math.floor((date.getTime() - now.getTime()) / (1000 * 60));
    
    if (Math.abs(diffInMinutes) < 60) {
      return rtf.format(diffInMinutes, 'minute');
    }
    
    const diffInHours = Math.floor(diffInMinutes / 60);
    if (Math.abs(diffInHours) < 24) {
      return rtf.format(diffInHours, 'hour');
    }
    
    const diffInDays = Math.floor(diffInHours / 24);
    return rtf.format(diffInDays, 'day');
  };

  return {
    locale: currentLocale,
    setLocale: setCurrentLocale,
    formatDate,
    formatNumber,
    formatCurrency,
    formatRelativeTime
  };
};

// 사용 예시
const App = () => {
  const { 
    locale, 
    setLocale, 
    formatDate, 
    formatCurrency, 
    formatRelativeTime 
  } = useIntl({ locale: 'ko-KR' });

  const price = 1234567;
  const saleDate = new Date();

  return (
    <div>
      <button onClick={() => setLocale('en-US')}>English</button>
      <button onClick={() => setLocale('ko-KR')}>한국어</button>
      
      <div>
        <p>가격: {formatCurrency(price, 'KRW')}</p>
        <p>판매일: {formatDate(saleDate, { 
          year: 'numeric', 
          month: 'long', 
          day: 'numeric' 
        })}</p>
        <p>등록: {formatRelativeTime(saleDate)}</p>
      </div>
    </div>
  );
};
```

### 2. 지역별 설정 관리

```javascript
// 지역별 설정 객체
const localeConfig = {
  'ko-KR': {
    currency: 'KRW',
    timezone: 'Asia/Seoul',
    dateFormat: {
      short: { year: 'numeric', month: 'numeric', day: 'numeric' },
      long: { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' }
    }
  },
  'en-US': {
    currency: 'USD',
    timezone: 'America/New_York',
    dateFormat: {
      short: { month: 'numeric', day: 'numeric', year: 'numeric' },
      long: { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' }
    }
  },
  'ja-JP': {
    currency: 'JPY',
    timezone: 'Asia/Tokyo',
    dateFormat: {
      short: { year: 'numeric', month: 'numeric', day: 'numeric' },
      long: { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' }
    }
  }
};

// 지역화 유틸리티 클래스
class LocalizationHelper {
  constructor(locale) {
    this.locale = locale;
    this.config = localeConfig[locale] || localeConfig['en-US'];
  }

  formatPrice(amount) {
    return new Intl.NumberFormat(this.locale, {
      style: 'currency',
      currency: this.config.currency,
      minimumFractionDigits: 0
    }).format(amount);
  }

  formatDate(date, format = 'short') {
    const options = this.config.dateFormat[format];
    return new Intl.DateTimeFormat(this.locale, options).format(date);
  }

  formatPercent(value) {
    return new Intl.NumberFormat(this.locale, {
      style: 'percent',
      minimumFractionDigits: 1,
      maximumFractionDigits: 1
    }).format(value);
  }
}

// React Context로 전역 관리
const LocaleContext = React.createContext();

const LocaleProvider = ({ children, initialLocale = 'ko-KR' }) => {
  const [locale, setLocale] = useState(initialLocale);
  const helper = new LocalizationHelper(locale);

  return (
    <LocaleContext.Provider value={{ locale, setLocale, helper }}>
      {children}
    </LocaleContext.Provider>
  );
};

const useLocale = () => {
  const context = useContext(LocaleContext);
  if (!context) {
    throw new Error('useLocale must be used within LocaleProvider');
  }
  return context;
};
```

### 3. 검색 및 정렬에 Intl.Collator 활용

```javascript
// 다국어 문자열 비교 (정렬)
const names = ['김철수', '이영희', '박민수', 'John', 'Alice', 'Bob'];

// 한국어 정렬 규칙 적용
const koreanCollator = new Intl.Collator('ko-KR');
const sortedKorean = names.sort(koreanCollator.compare);
console.log(sortedKorean); // 한글이 앞에, 영어가 뒤에

// 영어 정렬 규칙 적용
const englishCollator = new Intl.Collator('en-US');
const sortedEnglish = names.sort(englishCollator.compare);

// 대소문자 구분 없이 정렬
const caseInsensitiveCollator = new Intl.Collator('en-US', {
  sensitivity: 'base'  // 대소문자, 액센트 무시
});

// 검색 기능에 활용
const searchUsers = (users, searchTerm, locale = 'ko-KR') => {
  const collator = new Intl.Collator(locale, {
    sensitivity: 'base',
    ignorePunctuation: true
  });

  return users.filter(user => {
    return collator.compare(
      user.name.toLowerCase(), 
      searchTerm.toLowerCase()
    ) === 0 || user.name.toLowerCase().includes(searchTerm.toLowerCase());
  });
};
```

## 브라우저 호환성 및 주의사항

### 브라우저 지원 현황
- **모든 모던 브라우저 지원**: Chrome 24+, Firefox 29+, Safari 10+, Edge 12+
- **Node.js**: v12.0.0부터 전체 지원
- **Internet Explorer**: 부분 지원 (11에서 기본 기능만)

### 주의사항

```javascript
// 1. 지원하지 않는 로케일 처리
const safeFormatter = (locale, fallback = 'en-US') => {
  try {
    return new Intl.DateTimeFormat(locale);
  } catch (error) {
    console.warn(`Locale ${locale} not supported, using ${fallback}`);
    return new Intl.DateTimeFormat(fallback);
  }
};

// 2. 메모이제이션으로 성능 최적화
const formatters = new Map();

const getCachedFormatter = (locale, options) => {
  const key = `${locale}-${JSON.stringify(options)}`;
  
  if (!formatters.has(key)) {
    formatters.set(key, new Intl.DateTimeFormat(locale, options));
  }
  
  return formatters.get(key);
};

// 3. 타임존 처리
const formatWithTimezone = (date, locale, timezone) => {
  return new Intl.DateTimeFormat(locale, {
    timeZone: timezone,
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  }).format(date);
};
```

## 실무 적용 팁

### 1. Next.js에서 활용

```javascript
// pages/_app.js에서 locale 설정
export default function MyApp({ Component, pageProps, router }) {
  const { locale } = router;
  
  return (
    <LocaleProvider initialLocale={locale}>
      <Component {...pageProps} />
    </LocaleProvider>
  );
}

// SSR에서 서버와 클라이언트 시간 동기화
export async function getServerSideProps({ locale }) {
  const serverTime = new Date().toISOString();
  
  return {
    props: {
      serverTime,
      locale
    }
  };
}
```

### 2. 성능 최적화

```javascript
// 한 번만 생성하고 재사용
const FORMATTERS = {
  currency: {
    KRW: new Intl.NumberFormat('ko-KR', { style: 'currency', currency: 'KRW' }),
    USD: new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' })
  },
  date: {
    short: new Intl.DateTimeFormat('ko-KR', { 
      year: 'numeric', month: 'numeric', day: 'numeric' 
    })
  }
};

// 사용시 캐시된 포맷터 활용
const formatPrice = (amount, currency = 'KRW') => {
  return FORMATTERS.currency[currency].format(amount);
};
```

## 마무리

Intl API는 현대 웹 개발에서 필수적인 도구다. 외부 라이브러리 없이도 강력한 국제화 기능을 제공하며, 번들 크기를 줄이고 성능을 향상시킬 수 있다. 특히 글로벌 서비스를 개발할 때는 처음부터 Intl API를 활용한 설계를 고려하는 것이 좋다.

주요 장점:
- **표준 API**: 브라우저 내장으로 별도 설치 불필요
- **정확성**: 각 지역의 문화적 관습을 정확히 반영
- **성능**: 브라우저 엔진 레벨에서 최적화
- **유지보수**: 외부 라이브러리 의존성 제거

<br />

# 🔗 References
- [MDN - Intl API Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl)
- [JavaScript Intl API Guide](https://localizely.com/blog/javascript-intl-api/)
- [ECMAScript Internationalization API Specification](https://tc39.es/ecma402/)
- [Can I Use - Intl API Support](https://caniuse.com/internationalization)
- [Next.js Internationalization Guide](https://nextjs.org/docs/advanced-features/i18n)
