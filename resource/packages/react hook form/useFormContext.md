
### `useFormContext` 란

`useFormContext`는 `react-hook-form` 라이브러리에서 제공하는 커스텀 훅으로, 폼의 컨텍스트(context)에 접근할 수 있게 해줍니다. 이 훅은 특히 깊게 중첩된 컴포넌트 구조에서 유용합니다. 중첩된 컴포넌트에서 컨텍스트를 Prop으로 전달하는 대신, `useFormContext`를 사용하여 직접 폼의 컨텍스트에 접근할 수 있습니다.

---
### Return

`useForm` 훅이 반환하는 모든 메서드와 프로퍼티들을 반환합니다. 즉, `useForm`에서 사용하는 다양한 기능을 `useFormContext`를 통해 접근할 수 있습니다.

---
### 사용 방법

 `useFormContext`를 사용하기 위해서는 반드시 폼을 `FormProvider` 컴포넌트로 감싸야 합니다. `FormProvider`는 `useForm`의 반환값을 모든 하위 컴포넌트에 전달하는 역할을 합니다.

---
### 예시

```tsx
import React from "react";
import { useForm, FormProvider, useFormContext } from "react-hook-form";

export default function App() {
  const methods = useForm(); // useForm 훅을 사용하여 메서드와 프로퍼티들을 생성
  const onSubmit = (data) => console.log(data); // 폼 제출 시 호출될 함수

  return (
    <FormProvider {...methods}>
      {/* FormProvider를 사용하여 하위 컴포넌트에 폼 컨텍스트를 전달 */}
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NestedInput /> {/* 중첩된 입력 컴포넌트 */}
        <input type="submit" />
      </form>
    </FormProvider>
  );
}

function NestedInput() {
  const { register } = useFormContext(); // useFormContext를 사용하여 폼 메서드에 접근
  return <input {...register("test")} />; // 'test' 필드를 등록하고 해당 입력 필드로 연결
}
```

- 코드 설명

1. **`useForm`의 사용**:
   - `useForm` 훅을 호출하여 `methods` 객체를 생성합니다. 이 객체에는 `handleSubmit`, `register`, `setValue` 등 폼과 관련된 다양한 메서드가 포함되어 있습니다.

2. **`FormProvider`**:
   - `FormProvider`는 `useForm` 훅에서 반환된 `methods` 객체를 받아, 이를 하위 컴포넌트로 전달하는 역할을 합니다. 모든 하위 컴포넌트는 `useFormContext` 훅을 사용하여 이 메서드들을 직접 사용할 수 있습니다.

3. **`useFormContext`의 사용**:
   - `NestedInput` 컴포넌트에서 `useFormContext`를 호출하면, 상위에서 `FormProvider`로 전달된 `methods` 객체에 접근할 수 있습니다.
   - 여기서 `register` 메서드를 사용해 입력 필드를 폼에 등록하고, `name` 속성을 "test"로 지정합니다.
