오늘은 `TypeScript`에서 자주 마주치지만 때로는 혼란스러울 수 있는 세 가지 특별한 타입 - `any`, `unknown`, `never`에 대해 자세히 알아보겠습니다.

## any 타입: 자유롭지만 위험한 선택

`any` 타입은 `TypeScript`의 **타입 검사를 완전히 무시할 수 있게 해주는 특별한 타입**입니다. 말 그대로 "어떤 것이든" 허용한다는 의미죠.

```typescript
let anyValue: any = 42;
anyValue = "문자열도 OK";
anyValue = { whatever: "뭐든지 가능" };
anyValue.nonExistentMethod(); // 컴파일 에러 없음!
```

### any 사용 시 주의사항
- `TypeScript`의 타입 안정성을 완전히 포기하게 됩니다
- 런타임 에러의 위험이 높아집니다
- IDE의 자동완성 기능을 제대로 활용할 수 없습니다

### any가 유용한 경우
1. 레거시 `JavaScript` 코드를 점진적으로 `TypeScript`로 마이그레이션할 때
2. 타입을 알 수 없는 외부 라이브러리 사용 시
3. 프로토타입 개발 단계에서 빠른 개발이 필요할 때

---
## unknown 타입: 안전한 any

`unknown`은 `TypeScript` 3.0에서 도입된 타입으로, `any`의 타입 안전 버전이라고 할 수 있습니다.

```typescript
let unknownValue: unknown = 42;
unknownValue = "이것도 가능";
unknownValue = { hello: "world" };

// 다음 코드는 컴파일 에러 발생
// unknownValue.toUpperCase(); // Error
// unknownValue.foo.bar; // Error

// 타입 검사 후에만 사용 가능
if (typeof unknownValue === "string") {
    console.log(unknownValue.toUpperCase()); // OK
}
```

### unknown의 특징
- 어떤 값이든 할당 가능
- 직접적인 연산이나 메서드 호출 불가
- 타입 검사(`Type Guard`) 후에만 사용 가능

### unknown이 유용한 경우
1. API 응답 데이터처럼 형태를 미리 알 수 없는 데이터를 다룰 때
2. 타입 안전성을 유지하면서 동적 타입을 다뤄야 할 때
3. 에러 핸들링 시 에러의 타입이 불확실할 때

---
## never 타입: 발생할 수 없는 타입

`never` 타입은 절대 발생할 수 없는 타입을 나타냅니다. 

```typescript
// 항상 예외를 던지는 함수
function throwError(message: string): never {
    throw new Error(message);
}

// 무한 루프 함수
function infiniteLoop(): never {
    while (true) {}
}

// 타입 가드에서 있을 수 없는 상황을 처리
function processValue(value: string | number) {
    if (typeof value === "string") {
        // string 처리
    } else if (typeof value === "number") {
        // number 처리
    } else {
        // 여기서 value는 never 타입
        const exhaustiveCheck: never = value;
    }
}
```

### never의 특징
- 어떤 값도 가질 수 없음
- 모든 타입의 서브타입
- 어떤 값도 never 타입에 할당할 수 없음

### never가 유용한 경우
1. 절대 반환하지 않는 함수의 반환 타입 지정
2. 타입 가드에서 모든 케이스가 처리되었음을 보장
3. 유니온 타입에서 필터링된 타입을 표현할 때

---
## 실제 사용 예시

다음은 세 가지 타입을 실제로 활용하는 예시입니다:

```typescript
// API 응답을 처리하는 함수
async function processAPIResponse(endpoint: string): Promise<void> {
    try {
        const response: unknown = await fetch(endpoint).then(r => r.json());
        
        // 타입 가드를 통한 안전한 타입 체크
        if (typeof response === 'object' && response !== null) {
            // 여기서 response를 안전하게 사용
        }
    } catch (error: unknown) {
        // 에러 처리
        if (error instanceof Error) {
            console.error(error.message);
        } else {
            console.error('Unknown error occurred');
        }
    }
}

// 유니온 타입에서 never 활용
type NumericOperation = 'add' | 'subtract' | 'multiply' | 'divide';

function calculateNumbers(operation: NumericOperation, a: number, b: number): number {
    switch (operation) {
        case 'add':
            return a + b;
        case 'subtract':
            return a - b;
        case 'multiply':
            return a * b;
        case 'divide':
            if (b === 0) {
                throw new Error('Division by zero');
            }
            return a / b;
        default:
            // 모든 case를 처리했다는 것을 컴파일러에 알림
            const exhaustiveCheck: never = operation;
            return exhaustiveCheck;
    }
}
```
