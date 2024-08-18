### react hook form이란?

`react hook form`은 `React`에서 `form`을 쉽게 관리하고, **유효성 검사**를 간편하게 처리할 수 있도록 돕는 라이브러리입니다. 이 라이브러리는 기본적으로 React의 Hook 기능을 활용하여, 폼 데이터를 관리하고, 유효성 검사를 수행하며, 성능 최적화를 돕습니다. `Formik`이나 `Redux Form` 같은 다른 라이브러리와 비교했을 때, 코드량이 적고 사용하기 쉬운 것이 큰 장점입니다.

---

### 주요 특징

- **간단한 API**: `useForm`, `register`, `handleSubmit`와 같은 간단한 API로 `form`을 쉽게 관리할 수 있습니다.

- **가벼움**: `react hook form`은 다른 폼 관리 라이브러리와 비교했을 때 매우 가볍습니다. **불필요한 리렌더링을 최소화**하여 **애플리케이션의 성능을 최적화**합니다.

- **유효성 검사**:  기본적으로 제공되는 유효성 검사 기능 외에도, 기존의 유효성 검사 라이브러리(예: Yup)와 쉽게 통합할 수 있습니다.

- **조금의 코드로도 강력한 기능 구현**: 기존의 많은 코드를 줄이면서도 복잡한 폼 관리가 가능합니다.

- **타사 라이브러리와의 손쉬운 통합**: Material-UI, Ant Design 같은 UI 라이브러리와의 통합이 용이합니다.

-  **리액트 네이티브 지원**: `react hook form`은 React Native에서도 사용할 수 있어, 크로스 플랫폼 애플리케이션 개발에 유리합니다.
  
---

### 설치 

```bash
npm install react-hook-form
```

---

### 기본 사용법

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

function App() {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = data => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name", { required: true })} placeholder="Name" />
      {errors.name && <p>Name is required.</p>}

      <input {...register("email", { required: true, pattern: /^\S+@\S+$/i })} placeholder="Email" />
      {errors.email && <p>Invalid email address.</p>}

      <button type="submit">Submit</button>
    </form>
  );
}

export default App;
```

---

### 기본 유효성 검사 (Validation)

```jsx
<input
  {...register("password", {
    required: "Password is required",
    minLength: {
      value: 8,
      message: "Password must have at least 8 characters"
    }
  })}
/>
{errors.password && <p>{errors.password.message}</p>}
```

이처럼 `register` 함수의 두 번째 인자로 유효성 검사 규칙을 객체 형태로 전달할 수 있습니다. 각 규칙은 조건에 맞지 않을 경우 해당 메시지를 `errors` 객체에 추가합니다.

---

### form 상태 관리와 Watch

`react hook form`은 폼의 상태를 쉽게 추적할 수 있는 여러 기능을 제공합니다. `watch` 함수를 사용하면 사용자가 입력하는 값을 실시간으로 추적할 수 있습니다.

```jsx
const { register, watch } = useForm();
const watchAllFields = watch(); // 전체 필드 값 추적
```

`watch`를 사용하면 특정 필드나 전체 폼 데이터를 실시간으로 모니터링할 수 있습니다. 이를 활용해 입력 값에 따라 UI를 동적으로 변경할 수 있습니다.

---

### Controller와 타사 UI 라이브러리와의 통합

`react hook form`은 기본 HTML 입력 요소 외에도, `Material-UI`와 같은 **타사 UI 라이브러리와 통합**하여 사용할 수 있습니다. 이때 `Controller` 컴포넌트를 사용하면 됩니다.

```jsx
import React from 'react';
import { useForm, Controller } from 'react-hook-form';
import TextField from '@material-ui/core/TextField';

function App() {
  const { control, handleSubmit } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="firstName"
        control={control}
        defaultValue=""
        render={({ field }) => <TextField {...field} label="First Name" />}
      />
      <button type="submit">Submit</button>
    </form>
  );
}

export default App;
```

---
### `useController`를 사용한 타사 UI 라이브러리와의 통합

```jsx
import React from 'react';
import { useForm, useController } from 'react-hook-form';
import TextField from '@material-ui/core/TextField';

function ControlledTextField({ name, control, defaultValue, label }) {
  const {
    field: { onChange, onBlur, value, ref },
    fieldState: { error },
  } = useController({
    name,
    control,
    defaultValue,
  });

  return (
    <TextField
      label={label}
      value={value}
      onChange={onChange}
      onBlur={onBlur}
      inputRef={ref}
      error={!!error}
      helperText={error ? error.message : null}
    />
  );
}

function App() {
  const { control, handleSubmit } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <ControlledTextField
        name="firstName"
        control={control}
        defaultValue=""
        label="First Name"
      />
      <button type="submit">Submit</button>
    </form>
  );
}

export default App;
```

-  **useController**:  `Controller` 컴포넌트와 비슷하게 동작하지만, 더 낮은 수준의 제어를 제공합니다. `useController`를 사용하면 폼 필드의 상태(`value`, `onChange`, `onBlur` 등)와 유효성 검사 상태(`error`)에 직접 접근할 수 있습니다.

---

### 복잡한 form 관리하기
`React Hook Form`, `@hookform/resolvers`, `zod`를 활용하여 복잡한 폼을 관리하는 방법을 살펴보겠습니다. 이 예제에서는 `zod`를 사용하여 폼 입력에 대한 스키마 유효성 검사를 정의하고, `@hookform/resolvers`를 사용하여 `React Hook Form`과 `zod`를 통합하여 복잡한 폼 데이터를 관리합니다.

1.  설치

```bash
npm install react-hook-form @hookform/resolvers zod
```

2. 복잡한 폼 만들기

```tsx
import React from 'react';
import { useForm, SubmitHandler } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// 1. Zod 스키마 정의
const schema = z.object({
  firstName: z.string().min(1, { message: "First name is required" }),
  lastName: z.string().min(1, { message: "Last name is required" }),
  email: z.string().email({ message: "Invalid email address" }),
  age: z
    .number({ invalid_type_error: "Age must be a number" })
    .min(18, { message: "You must be at least 18 years old" }),
  address: z.object({
    street: z.string().min(1, { message: "Street address is required" }),
    city: z.string().min(1, { message: "City is required" }),
    zipCode: z.string().min(5, { message: "Zip code must be at least 5 characters" }),
  }),
  terms: z.boolean().refine((value) => value === true, {
    message: "You must accept the terms and conditions",
  }),
});

// Zod 스키마로부터 TypeScript 타입 생성
type FormData = z.infer<typeof schema>;

// 2. React Hook Form 설정
function ComplexForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<FormData>({
    resolver: zodResolver(schema),
  });

  const onSubmit: SubmitHandler<FormData> = (data) => {
    console.log("Form data:", data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>First Name:</label>
        <input {...register("firstName")} />
        {errors.firstName && <p>{errors.firstName.message}</p>}
      </div>

      <div>
        <label>Last Name:</label>
        <input {...register("lastName")} />
        {errors.lastName && <p>{errors.lastName.message}</p>}
      </div>

      <div>
        <label>Email:</label>
        <input type="email" {...register("email")} />
        {errors.email && <p>{errors.email.message}</p>}
      </div>

      <div>
        <label>Age:</label>
        <input type="number" {...register("age", { valueAsNumber: true })} />
        {errors.age && <p>{errors.age.message}</p>}
      </div>

      <fieldset>
        <legend>Address</legend>
        <div>
          <label>Street:</label>
          <input {...register("address.street")} />
          {errors.address?.street && <p>{errors.address.street.message}</p>}
        </div>

        <div>
          <label>City:</label>
          <input {...register("address.city")} />
          {errors.address?.city && <p>{errors.address.city.message}</p>}
        </div>

        <div>
          <label>Zip Code:</label>
          <input {...register("address.zipCode")} />
          {errors.address?.zipCode && <p>{errors.address.zipCode.message}</p>}
        </div>
      </fieldset>

      <div>
        <label>
          <input type="checkbox" {...register("terms")} />
          I accept the terms and conditions
        </label>
        {errors.terms && <p>{errors.terms.message}</p>}
      </div>

      <button type="submit">Submit</button>
    </form>
  );
}

export default ComplexForm;
```

3. 코드 설명

- Zod 스키마 정의

`zod`를 사용하여 **폼 데이터의 구조와 유효성 검사 규칙을 정의**합니다. 이 스키마는 폼의 각 필드가 어떤 타입의 데이터를 기대하며, 어떤 유효성 검사 규칙이 적용되는지를 나타냅니다.

- **firstName, lastName**: 필수 문자열 필드로, 최소 1자의 입력을 요구합니다.
- **email**: 이메일 형식을 요구하는 필수 문자열 필드입니다.
- **age**: 숫자 필드로, 최소 18세 이상이어야 합니다.
- **address**: 객체 형태로, 내부에 `street`, `city`, `zipCode` 필드를 포함합니다.
- **terms**: 체크박스로, 사용자가 동의해야만 유효한 값으로 처리됩니다.

- react hook form 설정

`useForm` 훅을 사용하여 폼 상태를 관리합니다. 여기서 `resolver` 옵션을 사용하여 `zodResolver`와 `zod` 스키마를 통합합니다. 이를 통해 폼 제출 시 `Zod` 스키마에 정의된 유효성 검사 규칙이 적용됩니다.

- **register**: 각 입력 필드를 폼에 등록하고, Zod 스키마에 따라 유효성 검사를 수행합니다.
- **handleSubmit**: 폼이 제출될 때 호출되는 함수입니다. 유효성 검사가 통과되면 `onSubmit` 함수가 호출됩니다.
- **formState.errors**: 유효성 검사를 통과하지 못한 필드에 대한 에러 메시지를 포함합니다.

- 폼 필드 및 유효성 검사 메시지

각 폼 필드는 `register` 함수를 통해 폼에 등록됩니다. 필드의 값이 `Zod` 스키마에 정의된 규칙을 위반할 경우, 해당 필드의 에러 메시지가 `errors` 객체에 추가됩니다. 이 에러 메시지를 폼 필드 아래에 표시하여 사용자에게 알려줍니다.

- 제출 및 결과 처리

폼이 유효하게 제출되면, `onSubmit` 함수가 호출되어 폼 데이터를 콘솔에 출력합니다. 유효성 검사를 통과하지 못한 필드는 자동으로 관련 메시지가 표시되어, 사용자가 올바르게 입력할 수 있도록 유도합니다.


