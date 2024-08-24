### useFieldArray 란?
`useFieldArray`는 `react-hook-form`에서 `Field Arrays`을 처리하기 위해 제공되는 커스텀 훅입니다. 이 훅은 **동적 폼 필드 배열을 관리**하는 데 있어 **성능 향상**과 더 나은 **사용자 경험**을 제공하도록 설계되었습니다. 

---

### Props

- **`name`** (string, 필수): 필드 배열의 이름입니다. 필수로 지정해야 하며, 동적 이름을 지원하지 않습니다.
  
- **`control`** (object, 선택): `useForm`에서 제공되는 control 객체입니다. 이 값은 `FormProvider`를 사용 중일 때는 선택 사항입니다.
  
- **`shouldUnregister`** (boolean, 선택): 필드 배열이 언마운트 후 해제될지 여부를 설정합니다.
  
- **`keyName`** (string, 기본값: `"id"`): 자동 생성된 식별자에 사용할 key 속성의 이름을 지정합니다. 이 속성은 다음 주요 버전에서 제거될 예정입니다.
  
- **`rules`** (object, 선택): 필드 배열에 대한 유효성 검사 규칙을 설정할 수 있습니다. `required`, `minLength`, `maxLength`, `validate`와 같은 검증 규칙이 포함됩니다.

---

### return

- **`fields`** (object & { id: string }): 각 필드의 기본값과 key를 포함하는 객체입니다.
  
- **`append`** (function): 필드 배열의 끝에 새로운 입력 필드를 추가하고 해당 필드로 포커스를 이동합니다.
  
- **`prepend`** (function): 필드 배열의 시작 부분에 새로운 입력 필드를 추가하고 해당 필드로 포커스를 이동합니다.
  
- **`insert`** (function): 특정 위치에 입력 필드를 삽입하고 해당 필드로 포커스를 이동합니다.
  
- **`swap`** (function): 두 입력 필드의 위치를 교환합니다.
  
- **`move`** (function): 입력 필드를 다른 위치로 이동시킵니다.
  
- **`update`** (function): 특정 위치에 있는 입력 필드를 업데이트합니다. 업데이트된 필드는 언마운트되고 다시 마운트됩니다.
  
- **`replace`** (function): 필드 배열의 전체 값을 교체합니다.
  
- **`remove`** (function): 특정 위치에 있는 입력 필드를 제거하거나, 인덱스가 지정되지 않은 경우 모든 필드를 제거합니다.

---
### rules

`useFieldArray`는 고유 식별자인 `id`를 자동으로 생성하며, 이는 `key` 속성으로 사용됩니다. 

- **필드의 `id`를 `key`로 사용해야 합니다 (`index`가 아님).** 이는 필드 재렌더링 시 필드가 깨지는 것을 방지하기 위함입니다.
  
  - ✅ 올바른 예시:
    ```jsx
    {fields.map((field, index) => <input key={field.id} ... />)}
    ```
  - ❌ 잘못된 예시:
    ```jsx
    {fields.map((field, index) => <input key={index} ... />)}
    ```

- **액션들을 연속으로 쌓아두지 않는 것이 좋습니다.**

  - ❌ 잘못된 예시:
    ```jsx
    onClick={() => {
      append({ test: 'test' });
      remove(0);
    }}
    ```
  - ✅ 더 나은 해결책: `remove` 액션이 두 번째 렌더링 후에 실행됩니다.
    ```jsx
    React.useEffect(() => {
      remove(0);
    }, [remove]);
    
    onClick={() => {
      append({ test: 'test' });
    }}
    ```

- **각 `useFieldArray`는 고유하며, 각 필드 배열은 개별적인 상태 업데이트를 가집니다.** 따라서 동일한 이름을 가진 여러 `useFieldArray`를 사용하지 않는 것이 좋습니다.

- **각 입력 필드의 이름은 고유해야 합니다.** 동일한 이름으로 체크박스나 라디오 버튼을 만들어야 하는 경우, `useController`나 `Controller`와 함께 사용해야 합니다.

- **평탄화된 필드 배열(flat field array)을 지원하지 않습니다.**

- **필드 배열을 추가(append), 앞에 추가(prepend), 삽입(insert), 업데이트(update)할 때, `obj`는 빈 객체일 수 없습니다.** 모든 입력 필드의 기본값(`defaultValues`)을 제공해야 합니다.

  - ❌ 잘못된 예시:
    ```jsx
    append(); // ❌
    append({}); // ❌
    ```
  - ✅ 올바른 예시:
    ```jsx
    append({ firstName: 'bill', lastName: 'luo' }); // ✅
    ```

### TypeScript 사용 시

- 입력 필드 이름을 등록할 때는 반드시 `const`로 캐스팅해야 합니다.
  ```jsx
  <input key={field.id} {...register(`test.${index}.test` as const)} />
  ```

- 순환 참조(circular reference)는 지원되지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/react-hook-form/react-hook-form/issues/1555)를 참고하세요.

- 중첩된 필드 배열을 사용할 경우, 필드 배열을 이름으로 캐스팅해야 합니다.
  ```jsx
  const { fields } = useFieldArray({ name: `test.${index}.keyValue` as 'test.0.keyValue' });
  ```
---
### 예제 코드

- 기본 사용 예제

```jsx
import React from "react";
import { useForm, useFieldArray } from "react-hook-form";

function FieldArray() {
  const { control, register } = useForm();
  const { fields, append, remove } = useFieldArray({
    control, // useForm에서 제공되는 control 객체
    name: "test", // 필드 배열의 고유 이름
  });

  return (
    <form>
      {fields.map((field, index) => (
        <div key={field.id}>
          <input {...register(`test.${index}.value`)} />
          <button type="button" onClick={() => remove(index)}>삭제</button>
        </div>
      ))}
      <button type="button" onClick={() => append({ value: "" })}>추가</button>
    </form>
  );
}
```

- 컨트롤된 필드 배열 예제

```jsx
import React from "react";
import { useForm, useFieldArray } from "react-hook-form";

function App() {
  const { control, register, watch } = useForm();
  const { fields, append } = useFieldArray({
    control,
    name: "fieldArray",
  });
  
  const watchFieldArray = watch("fieldArray");
  const controlledFields = fields.map((field, index) => ({
    ...field,
    ...watchFieldArray[index],
  }));

  return (
    <form>
      {controlledFields.map((field, index) => (
        <input key={field.id} {...register(`fieldArray.${index}.name`)} />
      ))}
      <button type="button" onClick={() => append({ name: "" })}>추가</button>
    </form>
  );
}

export default App;
```

---

### 사용 시 주의사항

- 각 `useFieldArray`는 고유하며 각 필드 배열의 상태 업데이트는 개별적으로 이루어집니다. 따라서 동일한 이름을 가진 여러 `useFieldArray`를 사용하지 않는 것이 좋습니다.
  
- 필드 배열의 각 입력 이름은 고유해야 합니다. 체크박스 또는 라디오 버튼과 같이 동일한 이름을 사용하는 입력을 생성하려면 `useController` 또는 `Controller`와 함께 사용해야 합니다.
  
- 필드 배열을 업데이트할 때는 모든 입력 필드의 기본값을 제공해야 하며, 빈 객체를 전달하지 않아야 합니다.