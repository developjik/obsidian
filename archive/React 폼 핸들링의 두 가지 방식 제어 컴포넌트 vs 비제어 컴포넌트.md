`React`에서 폼을 다루는 두 가지 주요 패턴인 `제어 컴포넌트(Controlled Components)`와 `비제어 컴포넌트(Uncontrolled Components)`에 대해 자세히 알아보겠습니다.

## 제어 컴포넌트(Controlled Components)란?

`제어 컴포넌트`는 **React가 폼 데이터를 완전히 제어하는 방식**입니다. **입력값이 React의 state에 의해 제어**되며, **모든 상태 변경은 setState를 통해** 이루어집니다.

### 제어 컴포넌트의 특징

1. `React` `state`가 **신뢰 가능한 단일 출처(single source of truth)**가 됩니다.
2. 입력값의 실시간 유효성 검사가 가능합니다.
3. 조건에 따른 입력값 변환이나 필터링이 용이합니다.
4. 폼 데이터의 즉각적인 반응이 필요한 경우에 적합합니다.

예시 코드를 보겠습니다:

```jsx
import React, { useState } from 'react';

function ControlledForm() {
  const [input, setInput] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('제출된 값:', input);
  };
  
  const handleChange = (e) => {
    setInput(e.target.value);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={input}
        onChange={handleChange}
        placeholder="이름을 입력하세요"
      />
      <button type="submit">제출</button>
    </form>
  );
}
```

---
## 비제어 컴포넌트(Uncontrolled Components)란?

`비제어 컴포넌트`는 **폼 데이터를 DOM 자체에서 관리하는 방식**입니다. `React`의 `state`를 사용하지 않고,` ref`를 통해 필요할 때만 폼 값을 가져옵니다.

### 비제어 컴포넌트의 특징

1. 구현이 더 간단하고 직관적입니다.
2. `React` `state` **업데이트로 인한 리렌더링이 없어 성능상 이점**이 있습니다.
3. 파일 입력과 같은 특정 입력 타입에 적합합니다.
4. 기존 코드베이스를 React로 통합할 때 유용합니다.

예시 코드를 살펴보겠습니다:

```jsx
import React, { useRef } from 'react';

function UncontrolledForm() {
  const inputRef = useRef();
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('제출된 값:', inputRef.current.value);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        ref={inputRef}
        defaultValue=""
        placeholder="이름을 입력하세요"
      />
      <button type="submit">제출</button>
    </form>
  );
}
```

---
## 언제 어떤 방식을 사용해야 할까요?

### 제어 컴포넌트를 사용해야 할 때

- 실시간 입력 유효성 검사가 필요한 경우
- 입력값에 따른 즉각적인 UI 업데이트가 필요한 경우
- 조건에 따라 입력을 비활성화해야 하는 경우
- 폼 데이터의 형식을 지정해야 하는 경우

### 비제어 컴포넌트를 사용해야 할 때

- 간단한 폼의 경우
- 파일 업로드 구현시
- 레거시 코드와의 통합이 필요한 경우
- 성능 최적화가 중요한 경우

---
## 실제 사용 예시: 복잡한 폼 구현하기

아래는 두 가지 방식을 모두 활용한 실제 사용 예시입니다:

```jsx
import React, { useState, useRef } from 'react';

function ComplexForm() {
  // 제어 컴포넌트용 state
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  
  // 비제어 컴포넌트용 ref
  const fileInputRef = useRef();
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // 폼 데이터 수집
    const formData = new FormData();
    formData.append('name', name);
    formData.append('email', email);
    formData.append('file', fileInputRef.current.files[0]);
    
    console.log('제출할 데이터:', {
      name,
      email,
      fileName: fileInputRef.current.files[0]?.name
    });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>이름:</label>
        <input
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
      </div>
      
      <div>
        <label>이메일:</label>
        <input
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
        />
      </div>
      
      <div>
        <label>파일:</label>
        <input
          type="file"
          ref={fileInputRef}
        />
      </div>
      
      <button type="submit">제출</button>
    </form>
  );
}
```
