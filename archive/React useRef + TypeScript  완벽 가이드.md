---
sticker: emoji//1f62b
---
안녕하세요! 오늘은 `React`의 `useRef`를 활용한 **입력 최적화 방법**과 `TypeScript` 통합에 대해 자세히 알아보겠습니다. 

## 📚 목차
1. `useRef` 개요와 사용 목적
2. 입력 최적화 구현하기
3. `TypeScript`와의 통합
4. 부모-자식 컴포넌트 간 DOM 제어
5. 실전 활용 예제와 커스텀 훅
6. 성능 최적화 전략
---
## 1. useRef 개요와 사용 목적

### useRef가 필요한 이유
`React`에서 상태 관리를 할 때 보통 `useState`를 사용합니다. 하지만 모든 상황에서 `useState`가 최선은 아닙니다. 특히 다음과 같은 경우에는 `useRef`가 더 적합할 수 있습니다:

- **빈번한 업데이트가 필요한 값**
- **렌더링에 영향을 주지 않아야 하는 값**
- **DOM 요소를 직접 조작해야 하는 경우**

### useState vs useRef 비교
```typescript
// useState 사용 시 - 매 입력마다 리렌더링 발생
const [value, setValue] = useState('');
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value); // 리렌더링 발생
};

// useRef 사용 시 - 리렌더링 없음
const valueRef = useRef('');
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  valueRef.current = e.target.value; // 리렌더링 없음
};
```

---
## 2. 입력 최적화 구현하기

### 기본적인 타입 정의
```typescript
interface InputChangeEvent extends React.ChangeEvent<HTMLInputElement> {
  target: HTMLInputElement;
}

interface OptimizedInputFormProps {
  onSubmit?: (value: string) => void;
  placeholder?: string;
}
```

### 최적화된 입력 컴포넌트
```typescript
const OptimizedInputForm: React.FC<OptimizedInputFormProps> = ({
  onSubmit,
  placeholder = '텍스트를 입력하세요'
}) => {
  const [renderCount, setRenderCount] = useState<number>(0);
  const inputRef = useRef<string>('');
  
  const handleChange = (e: InputChangeEvent): void => {
    inputRef.current = e.target.value;
  };
  
  const handleSubmit = (): void => {
    setRenderCount(prev => prev + 1);
    onSubmit?.(inputRef.current);
  };
  
  return (
    <div className="p-4">
      <input
        type="text"
        onChange={handleChange}
        placeholder={placeholder}
        className="border rounded p-2 w-full"
      />
      <button 
        onClick={handleSubmit}
        className="mt-2 bg-blue-500 text-white px-4 py-2 rounded"
      >
        제출
      </button>
      <div className="mt-2">렌더링 횟수: {renderCount}</div>
    </div>
  );
};
```

---
## 3. TypeScript와의 통합

### useRef의 타입 정의
```typescript
// DOM 요소 참조
const inputRef = useRef<HTMLInputElement>(null);

// 값 저장
const valueRef = useRef<string>('');

// 복잡한 객체 저장
interface UserData {
  name: string;
  age: number;
}
const userDataRef = useRef<UserData>({ name: '', age: 0 });
```

### 타입 안전성 확보
```typescript
// null 체크와 옵셔널 체이닝 활용
const focusInput = () => {
  inputRef.current?.focus();
};

// 타입 가드 활용
function isInputElement(element: unknown): element is HTMLInputElement {
  return element instanceof HTMLInputElement;
}

if (isInputElement(inputRef.current)) {
  inputRef.current.focus();
}
```

---
## 4. 부모-자식 컴포넌트 간 DOM 제어

### 컴포넌트 인터페이스 정의
```typescript
interface ChildInputProps {
  inputRef: React.RefObject<HTMLInputElement>;
  placeholder?: string;
}

interface ParentComponentProps {
  initialValue?: string;
}
```

### 구현 예제
```typescript
const ChildInput: React.FC<ChildInputProps> = ({ 
  inputRef, 
  placeholder 
}) => (
  <input
    ref={inputRef}
    type="text"
    placeholder={placeholder}
    className="border rounded p-2 w-full"
  />
);

const ParentComponent: React.FC<ParentComponentProps> = ({ 
  initialValue = '' 
}) => {
  const childInputRef = useRef<HTMLInputElement>(null);
  
  const handleSetValue = (): void => {
    if (childInputRef.current) {
      childInputRef.current.value = initialValue;
    }
  };
  
  return (
    <div>
      <ChildInput inputRef={childInputRef} />
      <button onClick={handleSetValue}>값 설정</button>
    </div>
  );
};
```

---
## 5. 실전 활용 예제와 커스텀 훅

### useRefInput 커스텀 훅
```typescript
interface UseRefInputReturn {
  value: string;
  setValue: (newValue: string) => void;
  reset: () => void;
  inputRef: React.RefObject<HTMLInputElement>;
}

function useRefInput(initialValue: string = ''): UseRefInputReturn {
  const inputRef = useRef<HTMLInputElement>(null);
  const valueRef = useRef<string>(initialValue);
  
  const setValue = (newValue: string): void => {
    valueRef.current = newValue;
    if (inputRef.current) {
      inputRef.current.value = newValue;
    }
  };
  
  const reset = (): void => {
    setValue(initialValue);
  };
  
  return {
    value: valueRef.current,
    setValue,
    reset,
    inputRef
  };
}
```

### 사용 예시
```typescript
const InputWithCustomHook: React.FC = () => {
  const { value, setValue, reset, inputRef } = useRefInput('초기값');
  
  return (
    <div className="space-y-2">
      <input
        ref={inputRef}
        type="text"
        onChange={(e) => setValue(e.target.value)}
        className="border rounded p-2"
      />
      <button 
        onClick={reset}
        className="bg-gray-500 text-white px-4 py-2 rounded"
      >
        초기화
      </button>
    </div>
  );
};
```

---
## 6. 성능 최적화 전략

### 메모이제이션 활용
```typescript
const MemoizedChildInput = React.memo(ChildInput);

// 사용
<MemoizedChildInput inputRef={inputRef} />
```

### 조건부 렌더링 최적화
```typescript
const ConditionalRender: React.FC = () => {
  const valueRef = useRef<string>('');
  const [shouldUpdate, setShouldUpdate] = useState(false);

  const handleChange = (e: InputChangeEvent) => {
    valueRef.current = e.target.value;
    // 특정 조건에서만 리렌더링
    if (e.target.value.length > 5) {
      setShouldUpdate(true);
    }
  };

  return (
    <input onChange={handleChange} />
  );
};
```

---
### 참고 자료
- [React 공식 문서 - useRef](https://reactjs.org/docs/hooks-reference.html#useref)
- [TypeScript 공식 문서](https://www.typescriptlang.org/docs/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)