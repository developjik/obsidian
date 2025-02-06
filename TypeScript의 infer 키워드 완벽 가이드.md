`TypeScript`의 강력한 **타입 추론 기능** 중 하나인 `infer` 키워드에 대해 자세히 알아보겠습니다. `infer` 키워드는 **조건부 타입에서 타입을 추론하고 추출하는 데 사용**되며, 복잡한 타입 시스템을 더욱 유연하게 만들어줍니다.

## infer란?

`infer` 키워드는 **조건부 타입 내에서 타입을 추론하고 해당 추론된 타입을 다른 곳에서 사용할 수 있게** 해주는 `TypeScript`의 특별한 키워드입니다. 주로 `extends` 키워드와 함께 사용되며, 복잡한 타입에서 특정 부분을 추출하는 데 매우 유용합니다.

---
## 기본 사용 예시

### 1. 배열 요소 타입 추출하기

```typescript
type ArrayElement<T> = T extends Array<infer U> ? U : never;

// 예제 1: 단순 문자열 배열
type StringArray = string[];
type ElementType = ArrayElement<StringArray>;
// 동작 과정:
// 1. StringArray는 Array<string>과 동일
// 2. Array<string>이 Array<infer U>와 매칭됨
// 3. U는 string으로 추론됨
// 결과: ElementType은 string

// 예제 2: 유니온 타입 배열
type MixedArray = (string | number)[];
type MixedElement = ArrayElement<MixedArray>;
// 동작 과정:
// 1. MixedArray는 Array<string | number>와 동일
// 2. Array<string | number>가 Array<infer U>와 매칭됨
// 3. U는 string | number로 추론됨
// 결과: MixedElement는 string | number

// 예제 3: 튜플
type TupleType = ArrayElement<[string, number, boolean]>;
// 동작 과정:
// 1. [string, number, boolean]은 Array의 하위 타입
// 2. infer U는 튜플의 모든 가능한 타입을 추론
// 결과: TupleType은 string | number | boolean
```

### 2. 함수 반환 타입 추출하기

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// 예제 1: 단순 함수
function greet(name: string): string {
    return `Hello, ${name}!`;
}
type GreetReturn = ReturnType<typeof greet>;
// 동작 과정:
// 1. typeof greet는 (name: string) => string 타입
// 2. 이 타입이 (...args: any[]) => infer R과 매칭됨
// 3. R은 string으로 추론됨
// 결과: GreetReturn은 string

// 예제 2: 제네릭 함수
function identity<T>(value: T): T {
    return value;
}
type IdentityReturn = ReturnType<typeof identity>;
// 동작 과정:
// 1. typeof identity는 <T>(value: T) => T 타입
// 2. 실제 반환 타입이 unknown으로 추론됨
// 결과: IdentityReturn은 unknown

// 예제 3: 복잡한 반환 타입
function processData(): { data: string[]; count: number } {
    return { data: [], count: 0 };
}
type ProcessReturn = ReturnType<typeof processData>;
// 동작 과정:
// 1. 함수의 반환 타입이 객체 타입으로 추론됨
// 결과: ProcessReturn은 { data: string[]; count: number }
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

### 3. 중첩된 Promise 타입 추출하기

```typescript
type UnwrapPromise<T> = T extends Promise<infer U> 
    ? U extends Promise<any> 
        ? UnwrapPromise<U> 
        : U 
    : T;
```

상세 사용 예제:

```typescript
// 예제 1: 단일 Promise
type SinglePromise = Promise<string>;
type Unwrapped1 = UnwrapPromise<SinglePromise>;
// 동작 과정:
// 1. Promise<string>이 Promise<infer U>와 매칭됨
// 2. U는 string으로 추론됨
// 3. string은 Promise가 아니므로 최종 결과
// 결과: Unwrapped1은 string

// 예제 2: 중첩된 Promise
type NestedPromise = Promise<Promise<number>>;
type Unwrapped2 = UnwrapPromise<NestedPromise>;
// 동작 과정:
// 1. 첫 번째 Promise 매칭: U는 Promise<number>
// 2. U가 Promise이므로 재귀적으로 다시 언래핑
// 3. 두 번째 Promise 매칭: U는 number
// 결과: Unwrapped2는 number

// 예제 3: 일반 타입
type NotPromise = string;
type Unwrapped3 = UnwrapPromise<NotPromise>;
// 동작 과정:
// 1. string은 Promise<infer U>와 매칭되지 않음
// 2. 조건이 false이므로 원본 타입 반환
// 결과: Unwrapped3은 string
```

### 4. 생성자 파라미터 타입 추출하기

```typescript
type ConstructorParameters<T> = T extends new (...args: infer P) => any ? P : never;
```

실제 사용 예제:

```typescript
// 예제 1: 기본 클래스 생성자
class User {
    constructor(name: string, age: number) {}
}
type UserConstructorParams = ConstructorParameters<typeof User>;
// 동작 과정:
// 1. typeof User는 생성자 타입을 나타냄
// 2. new (...args: infer P) => any와 매칭
// 3. P는 [name: string, age: number] 튜플로 추론
// 결과: UserConstructorParams는 [string, number]

// 예제 2: 선택적 매개변수가 있는 생성자
class Config {
    constructor(data: string, options?: { debug: boolean }) {}
}
type ConfigParams = ConstructorParameters<typeof Config>;
// 동작 과정:
// 1. 선택적 매개변수를 포함한 타입 추론
// 결과: ConfigParams는 [string, { debug: boolean }?]

// 예제 3: 제네릭 클래스
class Container<T> {
    constructor(value: T) {}
}
type ContainerParams = ConstructorParameters<typeof Container>;
// 동작 과정:
// 1. 제네릭 클래스의 생성자 타입 추론
// 결과: ContainerParams는 [unknown]
```

## 실전 활용 예제

### 1. API 응답 타입 처리

```typescript
// API 응답 기본 구조
type ApiResponse<T> = {
    data: T;
    status: number;
    message: string;
};

// 응답 데이터 타입 추출
type ExtractApiData<T> = T extends ApiResponse<infer U> ? U : never;

// 실제 사용 예제
interface UserData {
    id: number;
    name: string;
    email: string;
}

interface PostData {
    id: number;
    title: string;
    content: string;
}

// API 응답 타입 정의
type UserResponse = ApiResponse<UserData>;
type PostResponse = ApiResponse<PostData>;

// 데이터 타입 추출
type ExtractedUserData = ExtractApiData<UserResponse>;
type ExtractedPostData = ExtractApiData<PostResponse>;

// 사용 예시
const processApiResponse = <T>(response: ApiResponse<T>): ExtractApiData<typeof response> => {
    if (response.status === 200) {
        return response.data;
    }
    throw new Error(response.message);
};
```

### 2. 이벤트 핸들러 타입 처리

```typescript
// 이벤트 핸들러 타입 추출
type EventHandler<T> = T extends (...args: infer A) => void ? A : never;

// DOM 이벤트 핸들러 예제
function handleClick(event: MouseEvent) {}
function handleKeyPress(event: KeyboardEvent) {}
function handleSubmit(event: SubmitEvent, data: FormData) {}

// 이벤트 핸들러 파라미터 타입 추출
type ClickHandlerParams = EventHandler<typeof handleClick>;    // [MouseEvent]
type KeyHandlerParams = EventHandler<typeof handleKeyPress>;   // [KeyboardEvent]
type SubmitHandlerParams = EventHandler<typeof handleSubmit>; // [SubmitEvent, FormData]

// 실제 활용 예제
const createEventHandler = <T extends (...args: any[]) => void>(
    handler: T,
    logger: (params: EventHandler<T>) => void
) => {
    return (...args: EventHandler<T>) => {
        logger(args);
        handler(...args);
    };
};

// 사용 예시
const loggedClickHandler = createEventHandler(
    handleClick,
    (params) => console.log('Click event params:', params)
);
```

## 고급 활용 패턴

### 1. 조건부 타입과 함께 사용

```typescript
// 복잡한 타입 추출 예제
type ComplexExtract<T> = T extends Array<infer U>
    ? U extends object
        ? { [K in keyof U]: U[K] extends Function ? ReturnType<U[K]> : U[K] }
        : never
    : never;

// 사용 예시
interface Methods {
    getName(): string;
    getAge(): number;
    getData(): { id: string };
}

type ArrayOfMethods = Methods[];
type ExtractedTypes = ComplexExtract<ArrayOfMethods>;
```

### 2. 재귀적 타입 추출

```typescript
// 중첩된 객체에서 특정 키의 타입을 모두 추출
type DeepPropertyType<T, P> = P extends keyof T
    ? T[P]
    : T extends object
    ? T extends Array<any>
        ? never
        : { [K in keyof T]: DeepPropertyType<T[K], P> }[keyof T]
    : never;

// 사용 예시
interface DeepObject {
    id: number;
    user: {
        id: string;
        profile: {
            id: boolean;
        }
    };
    settings: {
        id: number;
    }
}

type AllIdTypes = DeepPropertyType<DeepObject, 'id'>; // number | string | boolean
```

