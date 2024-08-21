
### `useWatch` 란?

`useWatch`는 `react-hook-form` 라이브러리에서 제공하는 `Hook` 중 하나로, **폼의 특정 필드 값을 감시(watch)하고 해당 값이 변경될 때만 컴포넌트를 리렌더링하는 역할**을 합니다. `useWatch`는 `watch` API와 비슷하게 동작하지만, **커스텀 훅 수준에서 리렌더링을 격리하여 성능을 최적**화할 수 있습니다.

### 사용법
```tsx
useWatch({ name: 'inputName' }) // 특정 필드의 값 반환
useWatch({ name: ['inputName1'] }) // 여러 필드의 값 반환
useWatch() // 폼의 모든 필드 값을 키-값 쌍으로 반환
```

### 속성 (Props)
- **name**: 감시할 필드의 이름. 문자열 또는 문자열 배열로 지정할 수 있습니다.
- **control**: `useForm`에서 제공하는 control 객체. `FormProvider`를 사용하는 경우 선택 사항입니다.
- **defaultValue**: 초기 렌더링 전에 `useWatch`가 반환할 기본값.
- **disabled**: 구독을 비활성화할지 여부를 결정하는 옵션. 기본값은 `false`입니다.
- **exact**: 입력 이름 구독을 정확하게 일치시키는 옵션. 기본값은 `false`입니다.

### 반환값 (Return)
- `useWatch`의 반환값은 `defaultValue` 또는 `useForm`의 `defaultValues`에 정의된 값을 기준으로 합니다. 폼 필드의 값을 실시간으로 감시하며, 변경될 때마다 해당 필드만 리렌더링됩니다.

### 사용 시 주의사항
- `useWatch`의 실행 순서가 중요합니다. 구독(subscription)이 이루어지기 전에 폼의 값을 업데이트하면, 그 업데이트는 무시됩니다. 이 문제를 해결하려면, `useWatch`를 사용하여 폼 값을 구독하기 전에 값을 업데이트하지 않아야 합니다.
  
예시:
```tsx
setValue("test", "data")
useWatch({ name: "test" }) // ❌ 구독이 값 업데이트 이후에 발생하여 업데이트를 받지 못함
useWatch({ name: "example" }) // ✅ 구독이 먼저 이루어져 업데이트를 받음
setValue("example", "data")
```

### 커스텀 훅으로 해결 방법
위와 같은 문제를 해결하기 위해 다음과 같이 간단한 커스텀 훅을 사용할 수 있습니다:
```tsx
const useFormValues = () => {
  const { getValues } = useFormContext()

  return {
    ...useWatch(), // 폼 값 업데이트에 구독
    ...getValues(), // 최신 폼 값을 항상 병합
  }
}
```

### 사용 예시
#### 기본 폼 예시
```tsx
import React from "react"
import { useForm, useWatch } from "react-hook-form"

interface FormInputs {
  firstName: string
  lastName: string
}

function FirstNameWatched({ control }: { control: Control<FormInputs> }) {
  const firstName = useWatch({
    control,
    name: "firstName", // 특정 필드를 구독
    defaultValue: "default", // 초기 렌더링 시 기본값
  })

  return <p>Watch: {firstName}</p> // firstName이 변경될 때만 리렌더링
}

function App() {
  const { register, control, handleSubmit } = useForm<FormInputs>()

  const onSubmit = (data: FormInputs) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>First Name:</label>
      <input {...register("firstName")} />
      <input {...register("lastName")} />
      <input type="submit" />

      <FirstNameWatched control={control} />
    </form>
  )
}
```

#### 고급 필드 배열 예시
```tsx
import React from "react"
import { useWatch } from "react-hook-form"

function totalCal(results) {
  let totalValue = 0

  for (const key in results) {
    for (const value in results[key]) {
      if (typeof results[key][value] === "string") {
        const output = parseInt(results[key][value], 10)
        totalValue = totalValue + (Number.isNaN(output) ? 0 : output)
      } else {
        totalValue = totalValue + totalCal(results[key][value], totalValue)
      }
    }
  }

  return totalValue
}

export const Calc = ({ control, setValue }) => {
  const results = useWatch({ control, name: "test" })
  const output = totalCal(results)

  // 필드 배열에서 결과를 계산하여 리렌더링 격리
  console.log(results)

  setValue("total", output)

  return <p>{output}</p>
}
```

### 요약
`useWatch`는 `react-hook-form`에서 폼 필드의 변경 사항을 감시하고, 리렌더링을 최적화하는 데 유용한 훅입니다. 이를 활용하면 폼 성능을 개선할 수 있습니다.