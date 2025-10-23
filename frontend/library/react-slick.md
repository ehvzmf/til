> 📅 Date: 2025-10-23

# 📌 Focus
- React Slick 라이브러리
- 반응형 캐러셀/슬라이더 구현
- 커스텀 버튼 및 설정
- 슬라이더 최적화
<br />

# 📝 Learnings

## React Slick?
- jQuery의 Slick 카루셀을 React용으로 포팅한 라이브러리
- 반응형 슬라이더/캐러셀 간단히 구현 가능
- 다양한 커스터마이징 옵션 제공 

## 설치 및 기본 설정

### 1. 패키지 설치
```bash
# React Slick과 필수 CSS 설치
pnpm add react-slick slick-carousel
```

### 2. CSS 임포트
```tsx
// CSS 파일 임포트 (필수)
import "slick-carousel/slick/slick.css";
import "slick-carousel/slick/slick-theme.css";
```

## 기본 사용법

### 1. Simple Slider
```tsx
import Slider from 'react-slick';

const SimpleSlider = () => {
  const settings = {
    dots: true,          // 하단 점 네비게이션
    infinite: true,      // 무한 루프
    speed: 500,          // 애니메이션 속도 (ms)
    slidesToShow: 1,     // 보여질 슬라이드 개수
    slidesToScroll: 1,   // 스크롤할 슬라이드 개수
    autoplay: true,      // 자동 재생
    autoplaySpeed: 3000, // 자동 재생 간격 (ms)
  };

  return (
    <div>
      <Slider {...settings}>
        <div>
          <h3>Slide 1</h3>
        </div>
        <div>
          <h3>Slide 2</h3>
        </div>
        <div>
          <h3>Slide 3</h3>
        </div>
      </Slider>
    </div>
  );
};
```

### 2. 반응형 슬라이더 (핵심!)
```tsx
const ResponsiveSlider = () => {
  const settings = {
    dots: true,
    infinite: true,
    speed: 500,
    slidesToShow: 4,     // 기본: 4개 슬라이드
    slidesToScroll: 1,
    autoplay: true,
    autoplaySpeed: 4000,
    responsive: [
      {
        breakpoint: 1200,  // 1200px 이하
        settings: {
          slidesToShow: 3,
          slidesToScroll: 1,
        }
      },
      {
        breakpoint: 768,   // 768px 이하 (태블릿)
        settings: {
          slidesToShow: 2,
          slidesToScroll: 1,
          dots: true
        }
      },
      {
        breakpoint: 480,   // 480px 이하 (모바일)
        settings: {
          slidesToShow: 1,
          slidesToScroll: 1,
          dots: false,      // 모바일에서는 dots 숨기기
          arrows: false     // 모바일에서는 화살표 숨기기
        }
      }
    ]
  };

  return (
    <div className="slider-container">
      <Slider {...settings}>
        {items.map((item, index) => (
          <div key={index} className="slide-item">
            <img src={item.image} alt={item.title} />
            <h3>{item.title}</h3>
            <p>{item.description}</p>
          </div>
        ))}
      </Slider>
    </div>
  );
};
```

## 커스텀 네비게이션 버튼

### 1. 커스텀 화살표 버튼
```tsx
import { IoChevronBack, IoChevronForward } from 'react-icons/io5';

// 커스텀 이전 버튼
const CustomPrevArrow = ({ onClick, className }) => {
  return (
    <button
      className={`custom-arrow custom-prev ${className}`}
      onClick={onClick}
      aria-label="이전 슬라이드"
    >
      <IoChevronBack size={24} />
    </button>
  );
};

// 커스텀 다음 버튼  
const CustomNextArrow = ({ onClick, className }) => {
  return (
    <button
      className={`custom-arrow custom-next ${className}`}
      onClick={onClick}
      aria-label="다음 슬라이드"
    >
      <IoChevronForward size={24} />
    </button>
  );
};

// 슬라이더에 적용
const CustomSlider = () => {
  const settings = {
    dots: true,
    infinite: true,
    speed: 500,
    slidesToShow: 3,
    slidesToScroll: 1,
    prevArrow: <CustomPrevArrow />,
    nextArrow: <CustomNextArrow />,
    responsive: [
      // ... 반응형 설정
    ]
  };

  return (
    <div className="custom-slider">
      <Slider {...settings}>
        {/* 슬라이드 내용 */}
      </Slider>
    </div>
  );
};
```

### 2. 커스텀 화살표 CSS
```scss
.custom-slider {
  position: relative;
  
  // 커스텀 화살표 스타일
  .custom-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    z-index: 2;
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.9);
    border: 1px solid #e0e0e0;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;
    
    &:hover {
      background: white;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    }
    
    &.custom-prev {
      left: -24px;
    }
    
    &.custom-next {
      right: -24px;
    }
    
    // 모바일에서 화살표 위치 조정
    @media (max-width: 768px) {
      width: 40px;
      height: 40px;
      
      &.custom-prev {
        left: 10px;
      }
      
      &.custom-next {
        right: 10px;
      }
    }
  }
}
```

## 고급 설정 옵션

### 1. 다양한 슬라이더 설정
```tsx
const AdvancedSlider = () => {
  const settings = {
    // 기본 설정
    dots: true,
    dotsClass: "slick-dots custom-dots",  // 커스텀 dots 클래스
    infinite: true,
    speed: 500,
    fade: false,         // true시 페이드 효과
    cssEase: 'linear',   // 애니메이션 easing
    
    // 자동재생 설정
    autoplay: true,
    autoplaySpeed: 4000,
    pauseOnHover: true,  // 호버시 일시정지
    pauseOnFocus: true,  // 포커스시 일시정지
    
    // 스와이프 설정
    swipe: true,         // 터치 스와이프 허용
    swipeToSlide: true,  // 스와이프로 여러 슬라이드 이동
    touchThreshold: 10,  // 터치 감도
    
    // 중앙 정렬
    centerMode: true,    // 중앙 슬라이드 강조
    centerPadding: '20px', // 중앙모드 패딩
    
    // 지연 로딩
    lazyLoad: 'ondemand', // 'ondemand' | 'progressive'
    
    // 접근성
    accessibility: true,
    focusOnSelect: true, // 클릭한 슬라이드로 이동
    
    // 세로 슬라이더
    vertical: false,     // true시 세로 방향
    verticalSwiping: false,
    
    // 가변 너비
    variableWidth: false, // true시 슬라이드별 다른 너비
    
    // 이벤트 핸들러
    beforeChange: (current, next) => {
      console.log('슬라이드 변경 전:', current, '->', next);
    },
    afterChange: (current) => {
      console.log('현재 슬라이드:', current);
    },
    onInit: () => {
      console.log('슬라이더 초기화 완료');
    }
  };

  return <Slider {...settings}>{/* 슬라이드 내용 */}</Slider>;
};
```

### 2. 썸네일 슬라이더 (동기화된 슬라이더)
```tsx
import React, { useRef, useState } from 'react';

const ThumbnailSlider = ({ images }) => {
  const [nav1, setNav1] = useState(null);
  const [nav2, setNav2] = useState(null);
  const slider1 = useRef(null);
  const slider2 = useRef(null);

  useEffect(() => {
    setNav1(slider1.current);
    setNav2(slider2.current);
  }, []);

  // 메인 슬라이더 설정
  const mainSettings = {
    asNavFor: nav2,
    ref: slider1,
    slidesToShow: 1,
    slidesToScroll: 1,
    arrows: true,
    fade: true,
    adaptiveHeight: true
  };

  // 썸네일 슬라이더 설정
  const thumbSettings = {
    asNavFor: nav1,
    ref: slider2,
    slidesToShow: 4,
    slidesToScroll: 1,
    swipeToSlide: true,
    focusOnSelect: true,
    centerMode: true,
    responsive: [
      {
        breakpoint: 768,
        settings: {
          slidesToShow: 3
        }
      },
      {
        breakpoint: 480,
        settings: {
          slidesToShow: 2
        }
      }
    ]
  };

  return (
    <div className="thumbnail-slider">
      {/* 메인 슬라이더 */}
      <Slider {...mainSettings} className="main-slider">
        {images.map((img, index) => (
          <div key={index}>
            <img src={img.large} alt={`Slide ${index + 1}`} />
          </div>
        ))}
      </Slider>

      {/* 썸네일 슬라이더 */}
      <Slider {...thumbSettings} className="thumb-slider">
        {images.map((img, index) => (
          <div key={index}>
            <img src={img.thumb} alt={`Thumbnail ${index + 1}`} />
          </div>
        ))}
      </Slider>
    </div>
  );
};
```

## 반응형 디자인 베스트 프랙티스

### 1. 세밀한 반응형 설정
```tsx
const ResponsiveConfig = {
  dots: true,
  infinite: true,
  speed: 500,
  slidesToShow: 4,
  slidesToScroll: 1,
  responsive: [
    {
      breakpoint: 1400,  // 대형 데스크톱
      settings: {
        slidesToShow: 4,
        slidesToScroll: 2,
      }
    },
    {
      breakpoint: 1200,  // 데스크톱
      settings: {
        slidesToShow: 3,
        slidesToScroll: 1,
      }
    },
    {
      breakpoint: 992,   // 작은 데스크톱/큰 태블릿
      settings: {
        slidesToShow: 2,
        slidesToScroll: 1,
        centerMode: true,
        centerPadding: '40px'
      }
    },
    {
      breakpoint: 768,   // 태블릿
      settings: {
        slidesToShow: 2,
        slidesToScroll: 1,
        centerMode: false,
        dots: false,
        arrows: true
      }
    },
    {
      breakpoint: 576,   // 큰 모바일
      settings: {
        slidesToShow: 1,
        slidesToScroll: 1,
        centerMode: true,
        centerPadding: '60px',
        dots: false,
        arrows: false
      }
    },
    {
      breakpoint: 400,   // 작은 모바일
      settings: {
        slidesToShow: 1,
        slidesToScroll: 1,
        centerMode: true,
        centerPadding: '30px',
        dots: false,
        arrows: false
      }
    }
  ]
};
```

### 2. CSS로 반응형 보완
```scss
.slider-container {
  // 기본 컨테이너 설정
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  
  // 슬라이드 아이템 스타일
  .slide-item {
    padding: 0 10px; // 슬라이드 간 간격
    outline: none;   // 포커스 아웃라인 제거
    
    img {
      width: 100%;
      height: auto;
      border-radius: 8px;
      display: block;
    }
    
    h3 {
      margin: 10px 0 5px;
      font-size: 16px;
      
      @media (max-width: 768px) {
        font-size: 14px;
      }
    }
  }
  
  // dots 커스터마이징
  .slick-dots {
    bottom: -50px;
    
    li {
      margin: 0 5px;
      
      button:before {
        font-size: 12px;
        color: #ccc;
        opacity: 1;
      }
      
      &.slick-active button:before {
        color: #007bff;
      }
    }
    
    @media (max-width: 768px) {
      bottom: -30px;
      
      li button:before {
        font-size: 10px;
      }
    }
  }
  
  // 모바일에서 패딩 조정
  @media (max-width: 768px) {
    padding: 0 10px;
    
    .slide-item {
      padding: 0 5px;
    }
  }
}
```

## 성능 최적화

### 1. 이미지 지연 로딩
```tsx
const OptimizedSlider = () => {
  const settings = {
    // ... 기타 설정
    lazyLoad: 'ondemand',
    // 또는 'progressive' - 순차적으로 로드
  };

  return (
    <Slider {...settings}>
      {images.map((img, index) => (
        <div key={index}>
          {/* data-lazy 속성 사용 */}
          <img 
            data-lazy={img.src}
            alt={img.alt}
            className="lazy-image"
          />
        </div>
      ))}
    </Slider>
  );
};
```

### 2. 메모이제이션으로 리렌더링 방지
```tsx
const MemoizedSlider = React.memo(({ items, settings }) => {
  // settings를 useMemo로 메모이제이션
  const sliderSettings = useMemo(() => ({
    dots: true,
    infinite: true,
    speed: 500,
    ...settings
  }), [settings]);

  return (
    <Slider {...sliderSettings}>
      {items.map((item, index) => (
        <SlideItem key={item.id || index} item={item} />
      ))}
    </Slider>
  );
});

// 슬라이드 아이템도 메모이제이션
const SlideItem = React.memo(({ item }) => (
  <div className="slide-item">
    <img src={item.image} alt={item.title} />
    <h3>{item.title}</h3>
  </div>
));
```

## 커스텀 훅으로 슬라이더 로직 분리

```tsx
// useSlider.ts
import { useRef, useState, useCallback } from 'react';

interface UseSliderOptions {
  autoplay?: boolean;
  autoplaySpeed?: number;
}

export const useSlider = (options: UseSliderOptions = {}) => {
  const sliderRef = useRef<any>(null);
  const [currentSlide, setCurrentSlide] = useState(0);
  const [isPlaying, setIsPlaying] = useState(options.autoplay || false);

  // 다음 슬라이드로 이동
  const goToNext = useCallback(() => {
    sliderRef.current?.slickNext();
  }, []);

  // 이전 슬라이드로 이동
  const goToPrev = useCallback(() => {
    sliderRef.current?.slickPrev();
  }, []);

  // 특정 슬라이드로 이동
  const goToSlide = useCallback((index: number) => {
    sliderRef.current?.slickGoTo(index);
  }, []);

  // 자동재생 토글
  const toggleAutoplay = useCallback(() => {
    if (isPlaying) {
      sliderRef.current?.slickPause();
    } else {
      sliderRef.current?.slickPlay();
    }
    setIsPlaying(!isPlaying);
  }, [isPlaying]);

  // 슬라이드 변경 핸들러
  const handleSlideChange = useCallback((current: number) => {
    setCurrentSlide(current);
  }, []);

  return {
    sliderRef,
    currentSlide,
    isPlaying,
    goToNext,
    goToPrev,
    goToSlide,
    toggleAutoplay,
    handleSlideChange,
  };
};

// 사용 예시
const SliderComponent = ({ items }) => {
  const {
    sliderRef,
    currentSlide,
    isPlaying,
    goToNext,
    goToPrev,
    toggleAutoplay,
    handleSlideChange,
  } = useSlider({ autoplay: true, autoplaySpeed: 3000 });

  const settings = {
    // ... 기본 설정
    ref: sliderRef,
    afterChange: handleSlideChange,
  };

  return (
    <div className="slider-wrapper">
      <Slider {...settings}>
        {items.map((item, index) => (
          <SlideItem key={index} item={item} />
        ))}
      </Slider>
      
      {/* 커스텀 컨트롤 */}
      <div className="slider-controls">
        <button onClick={goToPrev}>이전</button>
        <button onClick={toggleAutoplay}>
          {isPlaying ? '일시정지' : '재생'}
        </button>
        <button onClick={goToNext}>다음</button>
        <span>{currentSlide + 1} / {items.length}</span>
      </div>
    </div>
  );
};
```

## 실무 팁

### 1. SSR 환경에서 주의사항
```tsx
import dynamic from 'next/dynamic';

// Next.js에서 SSR 문제 해결
const Slider = dynamic(() => import('react-slick'), {
  ssr: false,
  loading: () => <div>Loading...</div>
});

// 또는 useEffect로 클라이언트에서만 렌더링
const [mounted, setMounted] = useState(false);

useEffect(() => {
  setMounted(true);
}, []);

if (!mounted) return <div>Loading...</div>;

return <Slider {...settings}>{children}</Slider>;
```

### 2. TypeScript 타입 정의
```tsx
import { Settings } from 'react-slick';

interface CustomSliderProps {
  items: SlideItem[];
  settings?: Settings;
  className?: string;
}

const CustomSlider: React.FC<CustomSliderProps> = ({
  items,
  settings = {},
  className = ''
}) => {
  const defaultSettings: Settings = {
    dots: true,
    infinite: true,
    speed: 500,
    slidesToShow: 1,
    slidesToScroll: 1,
    ...settings
  };

  return (
    <div className={`slider-container ${className}`}>
      <Slider {...defaultSettings}>
        {items.map((item, index) => (
          <div key={index}>{/* 슬라이드 내용 */}</div>
        ))}
      </Slider>
    </div>
  );
};
```

### 3. 접근성 개선
```tsx
const AccessibleSlider = () => {
  const settings = {
    // ... 기본 설정
    accessibility: true,
    focusOnSelect: true,
    // ARIA 레이블 추가
    'aria-label': '이미지 슬라이더',
  };

  return (
    <div 
      className="slider-container"
      role="region"
      aria-label="제품 이미지 갤러리"
    >
      <Slider {...settings}>
        {items.map((item, index) => (
          <div
            key={index}
            role="tabpanel"
            aria-label={`${index + 1}번째 이미지: ${item.title}`}
          >
            <img 
              src={item.src} 
              alt={item.alt}
              role="img"
            />
          </div>
        ))}
      </Slider>
    </div>
  );
};
```

## 자주 겪는 문제와 해결법

### 1. 슬라이더 높이 불일치 문제
```scss
// 모든 슬라이드를 같은 높이로 맞추기
.slick-track {
  display: flex !important;
  align-items: stretch;
}

.slick-slide {
  height: inherit !important;
  
  > div {
    height: 100%;
    
    > div {
      height: 100%;
      display: flex;
      flex-direction: column;
    }
  }
}
```

### 2. 첫 번째 슬라이드 깜빡임 문제
```tsx
const [sliderReady, setSliderReady] = useState(false);

const settings = {
  // ... 기타 설정
  onInit: () => setSliderReady(true),
};

return (
  <div style={{ opacity: sliderReady ? 1 : 0 }}>
    <Slider {...settings}>
      {/* 슬라이드 내용 */}
    </Slider>
  </div>
);
```

### 3. 모바일에서 스크롤 간섭 문제
```tsx
const settings = {
  // ... 기타 설정
  swipe: true,
  touchThreshold: 10,        // 터치 감도 조정
  swipeToSlide: true,
  touchMove: true,
  useCSS: true,              // CSS transition 사용
  useTransform: true,        // transform 사용
};
```

<br />

# 🔗 References
- [React Slick 공식 문서](https://react-slick.neostack.com/)
- [Slick Carousel 원본 문서](https://kenwheeler.github.io/slick/)
- [React Slick GitHub](https://github.com/akiran/react-slick)
- [CSS Tricks - Slider Best Practices](https://css-tricks.com/slider-best-practices/)
- [Web Accessibility for Carousels](https://www.w3.org/WAI/tutorials/carousels/)
