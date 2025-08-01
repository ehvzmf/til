> 📅 Date: 2025-06-16

# 📌 Focus
- React-Quill findDOMNode 오류
- React 18/19 호환성 문제
- react-quill-new 마이그레이션
<br />

# 📝 Learnings

## findDOMNode 오류

React-Quill을 사용하면서 다음과 같은 오류가 발생하는 경우가 있다:

```
TypeError: react_dom_1.default.findDOMNode is not a function
```

또는 개발 모드에서 다음과 같은 경고 메시지가 나타난다:

```
Warning: findDOMNode is deprecated and will be removed in the next major release. 
Instead, add a ref directly to the element you want to reference.
```

- React 18 이상에서 StrictMode를 사용할 때
- Next.js 15와 React 19 환경에서 더욱 빈번하게 발생

<br />

## 문제 원인 분석

### 1. findDOMNode API의 deprecated 상태

**findDOMNode의 한계점:**

- **클래스 컴포넌트에서만 사용 가능**: 함수형 컴포넌트에서는 사용할 수 없음
- **실시간 업데이트 불가**: 호출 시점의 결과만 반환하고, 자식 컴포넌트가 나중에 다른 노드를 렌더링해도 알림받을 수 없음
- **성능 이슈**: DOM 트리를 탐색하는 과정에서 성능 오버헤드 발생
  
-> React 18.3부터 findDOMNode API가 deprecated되었고, React 19에서는 완전히 제거될 예정. 

<br />

### 2. React-Quill의 내부 구현 문제

기존 react-quill 패키지(v2.0.0)는 내부적으로 findDOMNode를 사용하여 Quill 인스턴스와 DOM 요소를 연결

- React 18의 Strict Mode에서 경고 발생
- React 19에서는 완전히 동작하지 않음
- Next.js 15와 같은 최신 프레임워크에서 호환성 문제

<br />

### 3. 패키지 업데이트 지연

원본 react-quill 패키지는 마지막 업데이트가 3년 전

 <br />

## 해결방법 1: react-quill-new로 마이그레이션

### react-quill-new

* 원본 react-quill의 포크 버전으로, findDOMNode 문제를 해결하고 최신 React 버전과 호환되도록 업데이트된 패키지.
* `npm install react-quill-new`

<br />

### 마이그레이션 과정

#### 1. Import 구문 변경

```typescript
// 기존 (react-quill)
import ReactQuill from 'react-quill';
import 'react-quill/dist/quill.snow.css';

// 변경 후 (react-quill-new)
import ReactQuill from 'react-quill-new';
import 'react-quill-new/dist/quill.snow.css';
```

<br />

#### 2. 기본 사용법 (거의 동일)

```typescript
import React, { useState } from 'react';
import ReactQuill from 'react-quill-new';
import 'react-quill-new/dist/quill.snow.css';

interface TextEditorProps {
  value: string;
  onChange: (content: string) => void;
  placeholder?: string;
}

const TextEditor: React.FC<TextEditorProps> = ({ 
  value, 
  onChange, 
  placeholder = "내용을 입력하세요..." 
}) => {
  return (
    <ReactQuill
      theme="snow"
      value={value}
      onChange={onChange}
      placeholder={placeholder}
    />
  );
};

// 사용 예시
const App: React.FC = () => {
  const [content, setContent] = useState<string>('');

  return (
    <div className="container">
      <h1>텍스트 에디터</h1>
      <TextEditor 
        value={content}
        onChange={setContent}
      />
      <div className="preview">
        <h2>미리보기:</h2>
        <div dangerouslySetInnerHTML={{ __html: content }} />
      </div>
    </div>
  );
};
```

<br />

## 고급 설정 및 커스터마이징

### 1. 툴바 커스터마이징

```typescript
import React, { useState } from 'react';
import ReactQuill from 'react-quill-new';

const CustomEditor: React.FC = () => {
  const [content, setContent] = useState('');

  // 커스텀 툴바 모듈 설정
  const modules = {
    toolbar: {
      container: [
        [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
        [{ 'font': [] }],
        [{ 'size': ['small', false, 'large', 'huge'] }],
        ['bold', 'italic', 'underline', 'strike'],
        [{ 'color': [] }, { 'background': [] }],
        [{ 'script': 'sub' }, { 'script': 'super' }],
        ['blockquote', 'code-block'],
        [{ 'list': 'ordered' }, { 'list': 'bullet' }],
        [{ 'indent': '-1' }, { 'indent': '+1' }],
        [{ 'direction': 'rtl' }],
        [{ 'align': [] }],
        ['link', 'image', 'video'],
        ['clean']
      ],
      // 커스텀 핸들러 추가 가능
      handlers: {
        'image': function() {
          const range = this.quill.getSelection();
          const value = prompt('이미지 URL을 입력하세요');
          if (value) {
            this.quill.insertEmbed(range.index, 'image', value, 'user');
          }
        }
      }
    },
    clipboard: {
      // 붙여넣기 시 스타일 제거 옵션
      matchVisual: false,
    }
  };

  // 허용할 포맷 지정
  const formats = [
    'header', 'font', 'size',
    'bold', 'italic', 'underline', 'strike',
    'color', 'background',
    'script',
    'blockquote', 'code-block',
    'list', 'bullet',
    'indent',
    'direction', 'align',
    'link', 'image', 'video'
  ];

  return (
    <ReactQuill
      theme="snow"
      value={content}
      onChange={setContent}
      modules={modules}
      formats={formats}
      placeholder="고급 에디터를 사용해보세요..."
    />
  );
};
```

<br />

### 2. 테마 및 스타일 커스터마이징

```typescript
// 버블 테마 사용
import 'react-quill-new/dist/quill.bubble.css';

const BubbleEditor: React.FC = () => {
  const [content, setContent] = useState('');

  return (
    <ReactQuill
      theme="bubble"  // 버블 테마 적용
      value={content}
      onChange={setContent}
      placeholder="텍스트를 선택하면 툴바가 나타납니다"
    />
  );
};

// 커스텀 CSS로 스타일링
const StyledEditor: React.FC = () => {
  return (
    <div className="custom-editor">
      <ReactQuill
        theme="snow"
        // ... 기타 props
      />
      <style jsx>{`
        .custom-editor .ql-toolbar {
          border-top: 1px solid #ccc;
          border-left: 1px solid #ccc;
          border-right: 1px solid #ccc;
          border-radius: 8px 8px 0 0;
          background-color: #f8f9fa;
        }
        
        .custom-editor .ql-container {
          border-bottom: 1px solid #ccc;
          border-left: 1px solid #ccc;
          border-right: 1px solid #ccc;
          border-radius: 0 0 8px 8px;
          font-family: 'Pretendard', sans-serif;
        }
        
        .custom-editor .ql-editor {
          min-height: 200px;
          font-size: 16px;
          line-height: 1.6;
        }
      `}</style>
    </div>
  );
};
```

<br />

### 3. 이벤트 핸들링 및 유효성 검사

```typescript
import React, { useState, useRef } from 'react';
import ReactQuill from 'react-quill-new';

interface EditorWithValidationProps {
  onSubmit: (content: string) => void;
}

const EditorWithValidation: React.FC<EditorWithValidationProps> = ({ onSubmit }) => {
  const [content, setContent] = useState('');
  const [error, setError] = useState<string>('');
  const quillRef = useRef<ReactQuill>(null);

  // 텍스트 길이 제한 (HTML 태그 제외)
  const MAX_LENGTH = 1000;

  const handleChange = (value: string) => {
    // HTML 태그를 제거하여 실제 텍스트 길이 계산
    const textLength = value.replace(/<[^>]*>/g, '').trim().length;
    
    if (textLength > MAX_LENGTH) {
      setError(`텍스트 길이는 ${MAX_LENGTH}자를 초과할 수 없습니다. (현재: ${textLength}자)`);
      return;
    }
    
    setError('');
    setContent(value);
  };

  const handleSubmit = () => {
    const textContent = content.replace(/<[^>]*>/g, '').trim();
    
    if (!textContent) {
      setError('내용을 입력해주세요.');
      return;
    }
    
    if (textContent.length < 10) {
      setError('최소 10자 이상 입력해주세요.');
      return;
    }
    
    onSubmit(content);
  };

  // 포커스 관련 이벤트
  const handleFocus = () => {
    console.log('Editor focused');
  };

  const handleBlur = () => {
    console.log('Editor blurred');
  };

  // 선택 영역 변경 이벤트
  const handleSelectionChange = (range: any, source: any, editor: any) => {
    if (range) {
      console.log('Selected range:', range);
    }
  };

  return (
    <div className="editor-container">
      <ReactQuill
        ref={quillRef}
        theme="snow"
        value={content}
        onChange={handleChange}
        onFocus={handleFocus}
        onBlur={handleBlur}
        onChangeSelection={handleSelectionChange}
        placeholder="최소 10자 이상 입력해주세요..."
      />
      
      {error && (
        <div className="error-message" style={{ color: 'red', marginTop: '8px' }}>
          {error}
        </div>
      )}
      
      <div className="editor-footer">
        <span className="char-count">
          {content.replace(/<[^>]*>/g, '').length} / {MAX_LENGTH}
        </span>
        <button 
          onClick={handleSubmit}
          disabled={!!error || !content.trim()}
          className="submit-button"
        >
          제출
        </button>
      </div>
    </div>
  );
};
```

<br />

### 4. 파일 업로드 및 이미지 처리

```typescript
import React, { useState } from 'react';
import ReactQuill from 'react-quill-new';

const EditorWithImageUpload: React.FC = () => {
  const [content, setContent] = useState('');

  // 이미지 업로드 핸들러
  const imageHandler = () => {
    const input = document.createElement('input');
    input.setAttribute('type', 'file');
    input.setAttribute('accept', 'image/*');
    input.click();

    input.onchange = async () => {
      const file = input.files?.[0];
      if (!file) return;

      // 파일 크기 제한 (2MB)
      if (file.size > 2 * 1024 * 1024) {
        alert('파일 크기는 2MB를 초과할 수 없습니다.');
        return;
      }

      try {
        // 여기서 실제 서버 업로드 로직 구현
        const imageUrl = await uploadImageToServer(file);
        
        // Quill 에디터에 이미지 삽입
        const quill = (document.querySelector('.ql-editor') as any).__quill;
        if (quill) {
          const range = quill.getSelection(true);
          quill.insertEmbed(range.index, 'image', imageUrl, 'user');
        }
      } catch (error) {
        console.error('이미지 업로드 실패:', error);
        alert('이미지 업로드에 실패했습니다.');
      }
    };
  };

  // 실제 구현에서는 서버 API 호출
  const uploadImageToServer = async (file: File): Promise<string> => {
    // FormData로 파일 전송
    const formData = new FormData();
    formData.append('image', file);

    const response = await fetch('/api/upload-image', {
      method: 'POST',
      body: formData,
    });

    if (!response.ok) {
      throw new Error('Upload failed');
    }

    const data = await response.json();
    return data.imageUrl;
  };

  const modules = {
    toolbar: {
      container: [
        [{ 'header': [1, 2, 3, false] }],
        ['bold', 'italic', 'underline'],
        ['link', 'image'],
        [{ 'align': [] }],
        ['clean']
      ],
      handlers: {
        image: imageHandler
      }
    }
  };

  return (
    <ReactQuill
      theme="snow"
      value={content}
      onChange={setContent}
      modules={modules}
      placeholder="이미지를 업로드해보세요..."
    />
  );
};
```

<br />

## 대안 해결방법들

### 1. React.StrictMode 비활성화

임시적인 해결책으로 StrictMode를 비활성화할 수 있지만, 권장하지 않는다:

```typescript
// 권장하지 않음
function App() {
  return (
    // <React.StrictMode> 제거
    <div className="App">
      <ReactQuill />
    </div>
    // </React.StrictMode>
  );
}
```

### 2. 다른 텍스트 에디터 라이브러리 사용

만약 react-quill-new도 문제가 있다면 다음 대안들을 고려할 수 있다:

- **react-quilljs**: Quill을 기반으로 한 또 다른 React 래퍼
- **@tinymce/tinymce-react**: TinyMCE 기반 에디터
- **draft-js**: Facebook에서 개발한 리치 텍스트 에디터
- **slate**: 완전히 커스터마이즈 가능한 프레임워크

```typescript
// react-quilljs 사용 예시
import { useQuill } from 'react-quilljs';
import 'quill/dist/quill.snow.css';

const AlternativeEditor = () => {
  const { quill, quillRef } = useQuill();

  React.useEffect(() => {
    if (quill) {
      quill.on('text-change', (delta, oldDelta, source) => {
        console.log('Text change!');
      });
    }
  }, [quill]);

  return <div ref={quillRef} />;
};
```

<br />

## Next.js에서의 특별 고려사항

### 1. 동적 import 사용

Next.js에서는 SSR 문제를 피하기 위해 동적 import를 사용하는 것이 좋다:

```typescript
import dynamic from 'next/dynamic';

// SSR 비활성화로 동적 import
const ReactQuill = dynamic(
  () => import('react-quill-new'),
  { 
    ssr: false,
    loading: () => <div>에디터 로딩중...</div>
  }
);

const MyEditor = () => {
  const [value, setValue] = useState('');
  
  return (
    <ReactQuill
      theme="snow"
      value={value}
      onChange={setValue}
    />
  );
};
```

<br />

### 2. CSS 스타일 import

Next.js에서 CSS import 위치에 주의:

```typescript
// _app.tsx에서 전역 스타일로 import
import 'react-quill-new/dist/quill.snow.css';

// 또는 컴포넌트에서 useEffect로 동적 로드
useEffect(() => {
  import('react-quill-new/dist/quill.snow.css');
}, []);
```

<br />

## 성능 최적화 팁

### 1. 메모이제이션 활용

```typescript
import React, { memo, useMemo } from 'react';

const OptimizedEditor = memo<EditorProps>(({ value, onChange, ...props }) => {
  const modules = useMemo(() => ({
    toolbar: [
      ['bold', 'italic', 'underline'],
      ['link', 'image'],
      [{ 'align': [] }]
    ]
  }), []);

  const formats = useMemo(() => [
    'bold', 'italic', 'underline', 'link', 'image', 'align'
  ], []);

  return (
    <ReactQuill
      value={value}
      onChange={onChange}
      modules={modules}
      formats={formats}
      {...props}
    />
  );
});
```

<br />

### 2. 디바운싱으로 onChange 최적화

```typescript
import { debounce } from 'lodash';

const DebouncedEditor = () => {
  const [content, setContent] = useState('');

  const debouncedOnChange = useMemo(
    () => debounce((value: string) => {
      setContent(value);
      // 서버에 자동 저장
      autoSave(value);
    }, 300),
    []
  );

  return (
    <ReactQuill
      defaultValue={content}
      onChange={debouncedOnChange}
    />
  );
};
```

<br />

# 🔗 References
- [react-quill GitHub Issues - findDOMNode deprecated](https://github.com/zenoamaro/react-quill/issues/972)
- [react-quill-new NPM Package](https://www.npmjs.com/package/react-quill-new)
- [React findDOMNode Documentation](https://18.react.dev/reference/react-dom/findDOMNode)
- [Quill.js Official Documentation](https://quilljs.com/docs/)
- [Next.js Dynamic Imports Guide](https://nextjs.org/docs/advanced-features/dynamic-import)
