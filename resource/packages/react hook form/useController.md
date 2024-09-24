### useController 란?

`useController`는 `Controller`를 지원하는 `커스텀 훅`입니다. 이는 `Controller`와 **동일한 속성과 메서드를 공유**하며, 재사용 가능한 Controlled input을 만들 때 유용합니다.

### Props

| 이름               | 타입              | 필수 여부 | 설명                                                                                                                                 |     |
| ---------------- | --------------- | ----- | ---------------------------------------------------------------------------------------------------------------------------------- | --- |
| name             | FieldPath       | ✓     | 입력의 고유한 이름                                                                                                                         |     |
| control          | Control         |       | `useForm`을 호출하여 제공된 제어 객체. `FormProvider`를 사용할 때는 선택 사항                                                                            |     |
| defaultValue     | unknown         |       | 중요한 점: `defaultValue` 또는 `defaultValues`에 `undefined`를 적용할 수 없습니다. `useForm`에 `defaultValues`를 제공해야 합니다. `undefined`는 유효한 값이 아닙니다. |     |
| rules            | Object          |       | `register`와 동일한 형식의 유효성 검사 규칙. 여기에는 `required`, `min`, `max`, `minLength`, `maxLength`, `pattern`, `validate` 등이 포함됩니다.            |     |
| shouldUnregister | boolean = false |       | 입력이 언마운트된 후 등록 취소되고 `defaultValues`도 제거됩니다.                                                                                        |     |
| disabled         | boolean = false |       | `field` 속성에서 반환되는 `disabled` 속성. 제어된 입력이 비활성화되며 제출 데이터에서 값이 생략됩니다.                                                                 |     |

### Return
`useController`가 생성하는 속성에 대한 정보를 포함하는 표입니다.

| 객체 이름 | 이름 | 타입 | 설명 |
| --- | --- | --- | --- |
| field | onChange | (value: any) => void | 입력의 값을 라이브러리에 보내는 함수. `onChange` prop에 할당해야 하며, 값은 `undefined`가 아니어야 합니다. 이 prop은 `formState`를 업데이트하며, 필드 업데이트와 관련된 다른 API를 수동으로 호출하는 것을 피해야 합니다. |
| field | onBlur | () => void | 입력의 `onBlur` 이벤트를 라이브러리에 보내는 함수. `onBlur` prop에 할당해야 합니다. |
| field | value | unknown | 제어된 컴포넌트의 현재 값. |
| field | disabled | boolean | 입력의 비활성화 상태. |
| field | name | string | 등록된 입력의 이름. |
| field | ref | React.Ref | 훅 폼을 입력과 연결하는 데 사용되는 참조. 오류 입력에 포커스를 맞추려면 이 참조를 컴포넌트의 입력 참조에 할당해야 합니다. |
| fieldState | invalid | boolean | 현재 입력의 유효하지 않은 상태. |
| fieldState | isTouched | boolean | 현재 제어된 입력의 터치 상태. |
| fieldState | isDirty | boolean | 현재 제어된 입력의 더티 상태. |
| fieldState | error | object | 특정 입력의 오류. |
| formState | isDirty | boolean | 사용자가 입력 중 하나를 수정한 후 `true`로 설정됩니다. |
| formState | dirtyFields | object | 사용자 수정된 필드의 객체. |
| formState | touchedFields | object | 사용자가 상호작용한 모든 입력을 포함하는 객체. |
| formState | defaultValues | object | `useForm`의 `defaultValues` 또는 `reset` API를 통해 업데이트된 `defaultValues` |
| formState | isSubmitted | boolean | 양식이 제출된 후 `true`로 설정됩니다. `reset` 메서드가 호출될 때까지 `true`로 유지됩니다. |
| formState | isSubmitSuccessful | boolean | 양식이 런타임 오류 없이 성공적으로 제출되었음을 나타냅니다. |
| formState | isSubmitting | boolean | 양식이 제출 중일 때 `true`로 설정됩니다. 그렇지 않으면 `false`입니다. |
| formState | isLoading | boolean | 비동기 `defaultValues`를 로드하는 중일 때 `true`로 설정됩니다. |
| formState | submitCount | number | 양식이 제출된 횟수. |
| formState | isValid | boolean | 양식에 오류가 없으면 `true`로 설정됩니다. `setError`는 `isValid` 상태에 영향을 미치지 않습니다. `isValid`는 항상 전체 양식의 유효성 검사 결과를 통해 파생됩니다. |
| formState | isValidating | boolean | 유효성 검사 중일 때 `true`로 설정됩니다. |
| formState | validatingFields | boolean | 비동기 유효성 검사를 받는 필드를 캡처합니다. |
| formState | errors | object | 필드 오류가 포함된 객체. 오류 메시지를 쉽게 검색할 수 있는 `ErrorMessage` 컴포넌트도 있습니다. |

### Examples

-  TextField 예시

```jsx
import { TextField } from "@material-ui/core";
import { useController, useForm } from "react-hook-form";

function Input({ control, name }) {
  const {
    field,
    fieldState: { invalid, isTouched, isDirty },
    formState: { touchedFields, dirtyFields }
  } = useController({
    name,
    control,
    rules: { required: true },
  });

  return (
    <TextField 
      onChange={field.onChange} // 값 전송
      onBlur={field.onBlur} // 블러 알림
      value={field.value} // 입력 값
      name={field.name} // 입력 이름
      inputRef={field.ref} // 입력 참조
    />
  );
}
```

### Tips

- 상태를 호스팅하고 `useController` 훅과 결합해 사용하는 것도 좋은 방법이다.

```typescript
const { field } = useController();
const [value, setValue] = useState(field.value);

onChange={(event) => {
  field.onChange(parseInt(event.target.value)) // 데이터를 훅 폼에 다시 보냄
  setValue(event.target.value) // UI 상태 업데이트
}}
```

-  입력 필드를 다시 등록하지 마세요.

```typescript
const { field } = useController({ name: 'test' })

<input {...field} /> // ✅ 올바른 방법
<input {...field} {...register('test')} /> // ❌ 이중 등록이 발생
```

- 컴포넌트당 하나의 `useController`를 사용하는 것이 이상적이다. 
  두 개 이상의 `useController`를 사용해야 할 경우 속성 이름을 변경해야 한다.
  이때는 `Controller`를 사용하는 것이 좋다.

```typescript
const { field: input } = useController({ name: 'test' })
const { field: checkbox } = useController({ name: 'test1' })

<input {...input} />
<input {...checkbox} />
```

