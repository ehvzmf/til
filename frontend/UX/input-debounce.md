> 📅 Date: 2025-04-21

# 📌 Focus
- Debounce
- 실무 활용
- 현재: 닉네임 관련 util에서 사용
  
<br />

# 📝 Learnings

## Debounce

> 짧은 시간 안에 연속적으로 발생하는 이벤트들을 묶어서, **마지막 이벤트만 실행되도록 제한**

- 버튼/스위치의 불필요한 중복 신호 제거 
- 예: 사용자 입력 이벤트 발생 시 → 입력이 멈춘 후 일정 시간이 지나면 실행
- API 요청 최적화, UI 반응 개선, 서버 부하 감소 등에 사용됨

---

## for What?
- **성능 최적화** | 입력할 때마다 요청 보내지 않도록 제어
- **사용자 경험 개선** | 입력 도중 응답으로 UI가 깜빡이지 않음
- **불필요한 트래픽 방지** | 연속 입력/이벤트 발생 시 요청 수를 대폭 줄일 수 있음

---

## ✅ 실무 활용

| 케이스 | 적용 이유 |
|--------|-----------|
| 🔍 검색 자동완성 | 입력할 때마다 API 요청하면 낭비됨 |
| 👤 닉네임/이메일 중복 확인 | 연속 입력 시 서버에 과도한 요청 방지 |
| 📊 필터 슬라이더 | 값이 바뀔 때마다 재랜더링 방지 |
| 📱 모바일 제스처 이벤트 | 연속 터치 이벤트 과잉 실행 제어 |

---

## 🛠 구현 방법

### 1. setTimeout을 직접 사용하는 방법

```tsx
useEffect(() => {
  const timeout = setTimeout(() => {
    if (nickname.length >= 2) {
      checkNicknameAvailability(nickname);
    }
  }, 500);

  return () => clearTimeout(timeout);
}, [nickname]);
```

---

### 2. `use-debounce` 라이브러리 활용

```bash
npm install use-debounce
```

```tsx
import { useDebounce } from 'use-debounce';

const [nickname, setNickname] = useState('');
const [debouncedNickname] = useDebounce(nickname, 500);

useEffect(() => {
  if (debouncedNickname.length >= 2) {
    checkNicknameAvailability(debouncedNickname);
  }
}, [debouncedNickname]);
```

---

### 3. lodash debounce 함수 사용

```bash
npm install lodash
```

```tsx
import { debounce } from 'lodash';

const handleChange = debounce((value) => {
  checkNicknameAvailability(value);
}, 500);
```

---

## 💡 닉네임 중복 확인 + Debounce + Tanstack Query 예제

```tsx
// api/checkNickname.ts
export const checkNickname = async (nickname: string): Promise<boolean> => {
  const res = await axiosInstance.get(`/check-nickname?value=${nickname}`);
  return res.data.isAvailable;
};
```

```tsx
// hooks/useNicknameCheck.ts
import { useQuery } from '@tanstack/react-query';
import { useDebounce } from 'use-debounce';
import { checkNickname } from '../api/checkNickname';

export const useNicknameCheck = (nickname: string) => {
  const [debouncedNickname] = useDebounce(nickname, 500);

  return useQuery({
    queryKey: ['nickname-check', debouncedNickname],
    queryFn: () => checkNickname(debouncedNickname),
    enabled: debouncedNickname.length >= 2,
  });
};
```

```tsx
// component
const NicknameInput = () => {
  const [nickname, setNickname] = useState('');
  const { data: isAvailable, isLoading } = useNicknameCheck(nickname);

  return (
    <div>
      <input value={nickname} onChange={(e) => setNickname(e.target.value)} />
      {isLoading && <p>확인 중...</p>}
      {isAvailable !== undefined && (
        <p>{isAvailable ? '사용 가능한 닉네임' : '이미 사용 중입니다'}</p>
      )}
    </div>
  );
};
```

# 🔗 References
- [Debounce 개념 (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout)
- [use-debounce](https://www.npmjs.com/package/use-debounce)
- [Tanstack Query](https://tanstack.com/query/v4)
