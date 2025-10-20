> 📅 Date: 2025-10-20

# 📌 Focus
- React Hook Form
- 성능 최적화된 폼 라이브러리
- 유효성 검사 및 폼 상태 관리
- TypeScript 지원
<br />

# 📝 Learnings

## React Hook Form이란?
- 폼 작성할 때 사용하는 라이브러리
- 최소한의 리렌더링으로 폼 관리
- 직관적인 API

## 주요 특징
- **성능 최적화**: 비제어 컴포넌트 방식으로 불필요한 리렌더링 최소화
- **최소한의 종속성**: 작은 번들 크기 (25KB)
- **TypeScript 완벽 지원**: 타입 안전성 보장
- **다양한 유효성 검사**: 내장 검증 및 외부 라이브러리 연동
- **직관적인 API**: 간단하고 명확한 사용법

## 기본 사용법

### 1. 설치
```bash
npm install react-hook-form
```

### 2. 기본 폼 구성
```tsx
import { useForm } from 'react-hook-form';

interface FormData {
  email: string;
  password: string;
}

function LoginForm() {
  const { 
    register, 
    handleSubmit, 
    formState: { errors } 
  } = useForm<FormData>();

  const onSubmit = (data: FormData) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('email', { 
          required: '이메일을 입력하세요',
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: '유효한 이메일을 입력하세요'
          }
        })}
        placeholder="이메일"
      />
      {errors.email && <span>{errors.email.message}</span>}

      <input
        type="password"
        {...register('password', { 
          required: '비밀번호를 입력하세요',
          minLength: {
            value: 6,
            message: '비밀번호는 6자 이상이어야 합니다'
          }
        })}
        placeholder="비밀번호"
      />
      {errors.password && <span>{errors.password.message}</span>}

      <button type="submit">로그인</button>
    </form>
  );
}
```

## 핵심 API

### 1. useForm Hook
```tsx
const {
  register,        // 입력 필드 등록
  handleSubmit,    // 폼 제출 핸들러
  watch,          // 필드 값 감시
  setValue,       // 필드 값 설정
  getValues,      // 현재 폼 값 조회
  reset,          // 폼 초기화
  formState       // 폼 상태 (errors, isValid, isDirty 등)
} = useForm<FormData>(options);
```

### 2. register 함수
- 입력 필드를 폼 등록
- 유효성 검사 규칙 설정

```tsx
<input
  {...register('fieldName', {
    required: '필수 입력 항목입니다',
    minLength: { value: 3, message: '최소 3자 이상' },
    maxLength: { value: 20, message: '최대 20자 이하' },
    pattern: { value: /regex/, message: '형식이 올바르지 않습니다' },
    validate: value => value !== 'admin' || '다른 값을 입력하세요'
  })}
/>
```

### 3. Controller (제어 컴포넌트용)
- 외부 UI 라이브러리/제어 컴포넌트와 함께 사용할 때 활용

```tsx
import { Controller } from 'react-hook-form';

<Controller
  name="category"
  control={control}
  rules={{ required: '카테고리를 선택하세요' }}
  render={({ field }) => (
    <Select
      {...field}
      options={categoryOptions}
      placeholder="카테고리 선택"
    />
  )}
/>
```

## 실무 활용 예시

### 1. 복잡한 폼 구조
```tsx
interface UserFormData {
  personal: {
    firstName: string;
    lastName: string;
    email: string;
  };
  preferences: {
    newsletter: boolean;
    notifications: string[];
  };
}

function UserForm() {
  const { register, handleSubmit, control, formState: { errors } } = useForm<UserFormData>();

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* 중첩 객체 */}
      <input {...register('personal.firstName', { required: true })} />
      <input {...register('personal.lastName', { required: true })} />
      <input {...register('personal.email', { required: true })} />

      {/* 체크박스 */}
      <input 
        type="checkbox" 
        {...register('preferences.newsletter')} 
      />

      {/* 배열 데이터 */}
      <Controller
        name="preferences.notifications"
        control={control}
        render={({ field }) => (
          <MultiSelect {...field} options={notificationOptions} />
        )}
      />
    </form>
  );
}
```

### 2. 동적 필드 추가/제거
```tsx
import { useFieldArray } from 'react-hook-form';

interface FormData {
  users: { name: string; email: string; }[];
}

function DynamicForm() {
  const { control, register, handleSubmit } = useForm<FormData>({
    defaultValues: {
      users: [{ name: '', email: '' }]
    }
  });

  const { fields, append, remove } = useFieldArray({
    control,
    name: 'users'
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {fields.map((field, index) => (
        <div key={field.id}>
          <input
            {...register(`users.${index}.name`, { required: true })}
            placeholder="이름"
          />
          <input
            {...register(`users.${index}.email`, { required: true })}
            placeholder="이메일"
          />
          <button type="button" onClick={() => remove(index)}>
            삭제
          </button>
        </div>
      ))}
      
      <button 
        type="button" 
        onClick={() => append({ name: '', email: '' })}
      >
        사용자 추가
      </button>
      
      <button type="submit">제출</button>
    </form>
  );
}
```

### 3. 외부 라이브러리와 통합 (Yup, Zod)
```tsx
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

const schema = yup.object({
  email: yup.string().email('유효한 이메일을 입력하세요').required('이메일은 필수입니다'),
  password: yup.string().min(6, '비밀번호는 6자 이상이어야 합니다').required('비밀번호는 필수입니다'),
  confirmPassword: yup.string()
    .oneOf([yup.ref('password')], '비밀번호가 일치하지 않습니다')
    .required('비밀번호 확인은 필수입니다')
});

function SignupForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(schema)
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input type="password" {...register('password')} />
      {errors.password && <span>{errors.password.message}</span>}
      
      <input type="password" {...register('confirmPassword')} />
      {errors.confirmPassword && <span>{errors.confirmPassword.message}</span>}
      
      <button type="submit">가입하기</button>
    </form>
  );
}
```

## 성능 최적화 팁

### 1. watch 사용 최소화
```tsx
// ❌ 전체 폼을 감시 (성능 저하)
const allValues = watch();

// ✅ 특정 필드만 감시
const email = watch('email');

// ✅ 여러 필드를 효율적으로 감시
const [email, password] = watch(['email', 'password']);
```

### 2. 조건부 렌더링 최적화
```tsx
// ❌ 매번 리렌더링
const isEmailValid = watch('email')?.includes('@');

// ✅ formState 활용
const { isValid } = formState;
```

### 3. 기본값 설정
```tsx
const { register } = useForm({
  defaultValues: {
    email: '',
    notifications: true,
    preferences: {
      theme: 'light'
    }
  }
});
```

## 실무 패턴

### 1. 커스텀 Hook으로 폼 로직 분리
```tsx
function useLoginForm() {
  const form = useForm<LoginFormData>({
    resolver: yupResolver(loginSchema)
  });

  const onSubmit = async (data: LoginFormData) => {
    try {
      await loginAPI(data);
      toast.success('로그인 성공');
    } catch (error) {
      toast.error('로그인 실패');
    }
  };

  return {
    ...form,
    onSubmit: form.handleSubmit(onSubmit)
  };
}

// 컴포넌트에서 사용
function LoginForm() {
  const { register, onSubmit, formState: { errors } } = useLoginForm();
  
  return (
    <form onSubmit={onSubmit}>
      {/* 폼 필드들 */}
    </form>
  );
}
```

### 2. 재사용 가능한 폼 컴포넌트
```tsx
interface FormFieldProps {
  name: string;
  label: string;
  type?: string;
  register: UseFormRegister<any>;
  error?: FieldError;
  rules?: RegisterOptions;
}

function FormField({ name, label, type = 'text', register, error, rules }: FormFieldProps) {
  return (
    <div className="form-field">
      <label htmlFor={name}>{label}</label>
      <input
        id={name}
        type={type}
        {...register(name, rules)}
        className={error ? 'error' : ''}
      />
      {error && <span className="error-message">{error.message}</span>}
    </div>
  );
}
```

## React Hook Form + Zod 실무 적용

### 1. Zod 스키마 정의
복잡한 유효성 검사 로직을 스키마로 분리해 따로 관리

```tsx
// schemas/signupSchema.ts
import { z } from "zod";

export const signUpSchema = z.object({
  nickname: z.string()
    .min(2, "2자 이상 입력해주세요.")
    .max(20, "20자 이하로 입력해주세요.")
    .regex(/^[\wㄱ-ㅎ가-힣]+$/, "특수문자는 사용할 수 없습니다."),
  age: z.enum(['20대', '30대', '40대', '50대', '60대 이상'], {
    required_error: "연령대를 선택해주세요.",
  }),
  gender: z.enum(['남성', '여성'], {
    required_error: "성별을 선택해주세요.",
  }),
  agree: z.literal(true, {
    errorMap: () => ({ message: "개인정보 수집 및 이용에 동의해주세요." }),
  }),
  ideology: z.enum(['중도', '보수', '진보']).optional(), // 선택하지 않으면 중도 처리
});

// 타입 추론
export type SignUpInput = z.infer<typeof signUpSchema>;
```

### 2. useForm과 Zod 연결
```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { signUpSchema, SignUpInput } from '@/shared/schema/signupSchema';

function SignUpForm() {
  const {
    register,
    handleSubmit,
    setValue,
    watch,
    formState: { errors, isValid },
  } = useForm<SignUpInput>({
    resolver: zodResolver(signUpSchema),
    mode: 'onChange', // 실시간 유효성 검사
  });

  const onSubmit = async (data: SignUpInput) => {
    // 폼 제출 로직
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* 폼 요소들 */}
    </form>
  );
}
```

### 3. MUI/커스텀 컴포넌트와 연결

#### TextField 연결
```tsx
import { TextField } from '@mui/material';

<TextField
  {...register("nickname")}
  label="닉네임"
  error={!!errors.nickname}
  helperText={errors.nickname?.message}
  fullWidth
/>
```

#### 커스텀 버튼 그룹 (연령대 선택)
```tsx
const ageOptions = ['20대', '30대', '40대', '50대', '60대 이상'];

<Box>
  {ageOptions.map((option) => (
    <Button
      key={option}
      onClick={() => setValue("age", option)}
      variant={watch("age") === option ? 'contained' : 'outlined'}
      sx={{ mr: 1, mb: 1 }}
    >
      {option}
    </Button>
  ))}
  {errors.age && (
    <Typography color="error" variant="caption">
      {errors.age.message}
    </Typography>
  )}
</Box>
```

#### 커스텀 선택 컴포넌트 (성별)
```tsx
const genderOptions = ['남성', '여성'];

<Box>
  {genderOptions.map((option) => (
    <Box
      key={option}
      onClick={() => setValue("gender", option)}
      sx={{
        p: 2,
        border: 1,
        borderColor: watch("gender") === option ? 'primary.main' : 'grey.300',
        backgroundColor: watch("gender") === option ? 'primary.light' : 'transparent',
        cursor: 'pointer',
        borderRadius: 1,
        mr: 1
      }}
    >
      {option}
    </Box>
  ))}
  {errors.gender && (
    <Typography color="error" variant="caption">
      {errors.gender.message}
    </Typography>
  )}
</Box>
```

#### 체크박스 (약관 동의)
```tsx
<Box
  onClick={() => setValue("agree", !watch("agree"))}
  sx={{
    display: 'flex',
    alignItems: 'center',
    cursor: 'pointer',
    p: 1
  }}
>
  <Checkbox checked={watch("agree") || false} />
  <Typography>개인정보 수집 및 이용에 동의합니다</Typography>
</Box>
{errors.agree && (
  <Typography color="error" variant="caption">
    {errors.agree.message}
  </Typography>
)}
```

### 4. 고급 활용 패턴

#### 조건부 유효성 검사
```tsx
const conditionalSchema = z.object({
  hasJob: z.boolean(),
  jobTitle: z.string().optional(),
  company: z.string().optional(),
}).refine((data) => {
  if (data.hasJob) {
    return data.jobTitle && data.company;
  }
  return true;
}, {
  message: "직업 정보를 모두 입력해주세요.",
  path: ["jobTitle"], // 에러를 표시할 필드
});
```

#### 비동기 유효성 검사 (닉네임 중복 체크)
```tsx
const checkNicknameDuplicate = async (nickname: string) => {
  try {
    const response = await api.checkNickname(nickname);
    return response.data.available;
  } catch {
    return false;
  }
};

// 컴포넌트 내부
const [nicknameChecking, setNicknameChecking] = useState(false);

const handleNicknameBlur = async () => {
  const nickname = watch("nickname");
  if (nickname && !errors.nickname) {
    setNicknameChecking(true);
    const isAvailable = await checkNicknameDuplicate(nickname);
    if (!isAvailable) {
      setError("nickname", { 
        type: "manual", 
        message: "이미 사용 중인 닉네임입니다." 
      });
    }
    setNicknameChecking(false);
  }
};
```

### 5. 완전한 제출 처리
```tsx
const onSubmit = async (data: SignUpInput) => {
  try {
    // 데이터 변환
    const payload = {
      ...data,
      age_group: mapAgeToCode(data.age),
      gender: data.gender === '여성',
      user_tendency: mapIdeologyToCode(data.ideology ?? '중도'),
      // 추가 필드들...
    };

    const response = await api.signup(payload);
    
    // 성공 처리
    localStorage.setItem('token', response.data.access_token);
    navigate('/signup/complete');
    
  } catch (error) {
    console.error('회원가입 실패:', error);
    // 에러 처리
    if (error.response?.data?.message) {
      setError("root", { 
        type: "manual", 
        message: error.response.data.message 
      });
    }
  }
};

// 유틸리티 함수들
const mapAgeToCode = (age: string) => {
  const ageMap = {
    '20대': 20, '30대': 30, '40대': 40, 
    '50대': 50, '60대 이상': 60
  };
  return ageMap[age];
};
```

### 6. 커스텀 Hook으로 추상화
```tsx
// hooks/useSignUpForm.ts
export const useSignUpForm = () => {
  const navigate = useNavigate();
  
  const form = useForm<SignUpInput>({
    resolver: zodResolver(signUpSchema),
    mode: 'onChange',
  });

  const [isSubmitting, setIsSubmitting] = useState(false);

  const onSubmit = async (data: SignUpInput) => {
    setIsSubmitting(true);
    try {
      // 제출 로직
      await submitSignUp(data);
      navigate('/signup/complete');
    } catch (error) {
      handleSubmitError(error, form.setError);
    } finally {
      setIsSubmitting(false);
    }
  };

  return {
    ...form,
    onSubmit: form.handleSubmit(onSubmit),
    isSubmitting,
  };
};

// 컴포넌트에서 사용
function SignUpForm() {
  const { register, setValue, watch, errors, onSubmit, isSubmitting } = useSignUpForm();
  
  return (
    <form onSubmit={onSubmit}>
      {/* 폼 요소들 */}
    </form>
  );
}
```

<br />

# 🔗 References
- [React Hook Form 공식 문서](https://react-hook-form.com/)
- [React Hook Form TypeScript 예제](https://react-hook-form.com/ts)
- [React Hook Form API Reference](https://react-hook-form.com/api)
- [Yup 연동 가이드](https://react-hook-form.com/get-started#SchemaValidation)
- [Zod 연동 가이드](https://github.com/react-hook-form/resolvers#zod)

