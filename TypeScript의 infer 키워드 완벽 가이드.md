`TypeScript`의 강력한 **타입 추론 기능** 중 하나인 `infer` 키워드에 대해 자세히 알아보겠습니다. `infer` 키워드는 **조건부 타입에서 타입을 추론하고 추출하는 데 사용**되며, 복잡한 타입 시스템을 더욱 유연하게 만들어줍니다.

## infer란?

`infer` 키워드는 **조건부 타입 내에서 타입을 추론하고 해당 추론된 타입을 다른 곳에서 사용할 수 있게** 해주는 `TypeScript`의 특별한 키워드입니다. 주로 `extends` 키워드와 함께 사용되며, 복잡한 타입에서 특정 부분을 추출하는 데 매우 유용합니다.

---
## 기본 사용 예시

### 1. 배열의 요소 타입 추출하기

```typescript
type ArrayElement<T> = T extends Array<infer U> ? U : never;

// 사용 예시
type StringArray = string[];
type ElementType = ArrayElement<StringArray>; // string

type NumberArray = Array<number>;
type NumberType = ArrayElement<NumberArray>; // number

// 튜플에서도 작동합니다
type TupleType = ArrayElement<[string, number]>; // string | number
```

### 2. 함수의 반환 타입 추출하기

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// 사용 예시
function getMessage(): string {
    return "Hello, World!";
}

type MessageType = ReturnType<typeof getMessage>; // string

const addNumbers = (a: number, b: number) => a + b;
type AddReturn = ReturnType<typeof addNumbers>; // number
```

### 3. 함수의 매개변수 타입 추출하기

```typescript
type FirstParameter<T> = T extends (first: infer P, ...args: any[]) => any ? P : never;

// 사용 예시
function processUser(id: number, name: string) {
    // 함수 구현
}

type FirstParam = FirstParameter<typeof processUser>; // number
```

## 고급 사용 예시

### 1. Promise 결과 타입 추출하기

```typescript
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

// 사용 예시
type PromiseString = Promise<string>;
type UnwrappedString = UnwrapPromise<PromiseString>; // string

type RegularType = UnwrapPromise<number>; // number (Promise가 아닌 경우 원래 타입 반환)
```

### 2. 중첩된 객체에서 특정 타입 추출하기

```typescript
type DeepPropertyType<T, P> = P extends keyof T
    ? T[P]
    : T extends object
    ? T extends Array<any>
        ? never
        : { [K in keyof T]: DeepPropertyType<T[K], P> }[keyof T]
    : never;

// 사용 예시
interface User {
    id: number;
    info: {
        name: string;
        settings: {
            theme: string;
        }
    }
}

type ThemeType = DeepPropertyType<User, 'theme'>; // string
```

### 3. 생성자 매개변수 타입 추출하기

```typescript
type ConstructorParameters<T> = T extends new (...args: infer P) => any ? P : never;

// 사용 예시
class Person {
    constructor(name: string, age: number) {}
}

type PersonConstructorParams = ConstructorParameters<typeof Person>; // [string, number]
```

## 실전 활용 사례

### 1. API 응답 타입 추론하기

```typescript
type ApiResponse<T> = {
    data: T;
    status: number;
    message: string;
};

type ExtractApiData<T> = T extends ApiResponse<infer U> ? U : never;

// 사용 예시
interface UserData {
    id: number;
    name: string;
}

type UserResponse = ApiResponse<UserData>;
type ExtractedUserData = ExtractApiData<UserResponse>; // UserData
```

### 2. 이벤트 핸들러 타입 추출하기

```typescript
type EventHandler<T> = T extends (...args: infer A) => void ? A : never;

// 사용 예시
function onClick(event: MouseEvent) {}
function onKeyPress(event: KeyboardEvent) {}

type ClickHandlerParams = EventHandler<typeof onClick>; // [MouseEvent]
type KeyHandlerParams = EventHandler<typeof onKeyPress>; // [KeyboardEvent]
```

---
## 주의사항 및 팁

1. `infer`는 반드시 **조건부 타입**(`extends` 조건문) 내에서만 사용할 수 있습니다.
2. 하나의 조건부 타입에서 여러 개의 `infer`를 사용할 수 있습니다.
3. 추론된 타입이 없는 경우 조건부 타입의 `false` 분기가 선택됩니다.
