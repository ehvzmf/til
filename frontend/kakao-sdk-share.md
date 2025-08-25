> 📅 Date: 2025-08-25

# 📌 Focus
- 카카오톡 공유 기능 만들기
- Javascript sdk 사용

<br />

# 📝 Learnings
### 구현하기 전 결정해야 할 사항
- 구현 방식 > 직접 만든 버튼
- 메세지 타입은? > 피드
- 미리 이미지 업로드

### 설치
> 설치 전 플랫폼에서 앱 등록하기! <br />
> 카카오 로그인 때문에 등록해놨으므로 패스 
```html
<script src="https://t1.kakaocdn.net/kakao_js_sdk/${VERSION}/kakao.min.js"
  integrity="${INTEGRITY_VALUE}" crossorigin="anonymous"></script>
```
- [다운로드](https://developers.kakao.com/docs/latest/ko/javascript/download)에서 버전에 따라 바로 버튼으로 복사 가능
- 최신 버전 사용 (2.7.6)

### 초기화
```js
Kakao.init('JAVASCRIPT_KEY');
Kakao.isInitialized();
```
- Javascript SDK 초기화 함수
- 앱 키에서 Javascript Key를 찾아 `.env`에 추가

<details>
<summary>kakaoShare.ts</summary>

```js

declare global {
  interface Window {
    Kakao: {
      init: (key: string) => void;
      isInitialized: () => boolean;
      Share: {
        sendDefault: (options: {
          objectType: string;
          content: {
            title: string;
            description: string;
            imageUrl: string;
            link: {
              mobileWebUrl: string;
              webUrl: string;
            };
          };
          buttons?: Array<{
            title: string;
            link: {
              mobileWebUrl: string;
              webUrl: string;
            };
          }>;
        }) => void;
      };
    };
  }
}

const KAKAO_APP_KEY = import.meta.env.VITE_KAKAO_JAVASCRIPT_KEY;

export const initializeKakao = () => {
  if (typeof window !== 'undefined' && window.Kakao) {
    if (!window.Kakao.isInitialized()) {
      window.Kakao.init(KAKAO_APP_KEY);
      console.log('Kakao SDK 초기화 완료');
    }
    return true;
  }
  return false;
};

export const shareToKakao = (options: {
  title: string;
  description: string;
  imageUrl: string;
  webUrl?: string;
}) => {
  if (!initializeKakao()) {
    console.error('Kakao SDK가 로드되지 않았습니다.');
    alert('카카오톡 공유 기능을 사용할 수 없습니다.');
    return;
  }

  const currentUrl = options.webUrl || window.location.href;

  try {
    window.Kakao.Share.sendDefault({
      objectType: 'feed',
      content: {
        title: options.title,
        description: options.description,
        imageUrl: options.imageUrl,
        link: {
          mobileWebUrl: currentUrl,
          webUrl: currentUrl,
        },
      },
      buttons: [
        {
          title: '나도 테스트하기',
          link: {
            mobileWebUrl: currentUrl,
            webUrl: currentUrl,
          },
        },
      ],
    });
  } catch (error) {
    console.error('카카오톡 공유 실패:', error);
    alert('카카오톡 공유에 실패했습니다.');
  }
};

```

</details>

<br />

# 🔗 References
- [Kakao Developers](https://developers.kakao.com/docs/latest/ko/kakaotalk-share/js-link)
