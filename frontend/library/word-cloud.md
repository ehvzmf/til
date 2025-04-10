> 📅 Date: 2025-04-10

# 📌 Focus
- How to build word cloud ui
  ![image](https://github.com/user-attachments/assets/e82a43eb-b7ff-433a-958b-94485ee08e58)
<br />

# 📝 Learnings
### react-wordcloud
- React 친화적
- 기본 제공 옵션이 많아 편리
- 커스터마이징 한계
- 라이브러리 업데이트

### d3-cloud
- d3 기반 라이브러리
- 더 세밀한 커스터마이징
- 추가 설정이 많음

### echarts-wordcloud
- 공식 확장 플러그인
- echarts와의 일관성 (현재 프로젝트에서 사용 중이므로)
- 강력한 옵션과 애니메이션
- 플러그인 형태
- 무거운 라이브러리
- 리액트 연동 시 추가 래퍼 라이브러리를 사용해야 할 수도
- 기본적으로 Vanilla JS library
- 플러그인 의존성

- `npm install echarts-wordcloud --save`
- import: 
```
import * as echarts from 'echarts';
import 'echarts-wordcloud';
```

<br />

# 🔗 References
- [react-wordcloud](https://www.npmjs.com/package/react-wordcloud)
- [d3-cloud](https://github.com/jasondavies/d3-cloud)
- [echarts-wordcloud](https://github.com/ecomfe/echarts-wordcloud)
