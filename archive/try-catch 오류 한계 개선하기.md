## try/catch 개선하기

### 1️⃣ 유틸리티 fn

- Promise를 감싸는 재사용 가능한 utility fn
    
    - 이 함수는 [error, result] 튜플을 반환하여 오류와 결과를 명시적으로 구분합니다.
    
    ```tsx
    async function handleAsync<T>(promise: Promise<T>): Promise<[Error | null, T | null]> {
      try {
        const result = await promise;
        return [null, result];
      } catch (error) {
        return [error instanceof Error ? error : new Error("Unknown error"), null];
      }
    }
    ```
    
    ```tsx
    const [error, data] = await handleAsync(fetchUserData());
    if (error) {
      console.error(error.message);
      return;
    }
    console.log(data); *// 타입 안전성 보장*
    ```
    
- **동작**: 이 함수는 Promise를 받아서 [error, result] 튜플을 반환합니다.
    
    - 성공 시: [null, 값]
    - 실패 시: [Error 객체, null]
- **장점**:
    
    - **간단함**: try/catch를 한 번 감싸는 유틸리티로, 반복 코드를 줄입니다.
    - **명시적 반환**: 오류와 결과를 튜플로 구분해서 반환하니 호출자가 성공/실패를 쉽게 체크할 수 있습니다.
    - **타입 지원**: 제네릭 <T>를 사용해 결과 타입을 보장합니다.
    - **충분한 경우**: 단순한 비동기 호출에서 성공/실패만 구분하면 되는 상황이라면 이 정도면 충분합니다.
- 한계
    
    **❌ 타입 강제성 부족**:
    
    - 반환 타입이 [Error | null, T | null]이라서 호출자가 if (error)로 체크해야 하지만, 이를 강제하지 않습니다. 실수로 data에 바로 접근해도 컴파일러가 막지 못합니다:
        
        ```rust
        const [error, data] = await handleAsync(somePromise);
        console.log(data.toString()); *// data가 null일 수 있는데도 오류 안 남*
        ```
        
    
    **❌ 확장성**:
    
    - 복잡한 로직에서 성공/실패 이후 추가 작업(예: 값 변환, 연쇄 호출)을 처리하려면 호출자 측에서 계속 분기 처리를 해야 해서 코드가 길어질 수 있습니다.

### 2️⃣ Result 타입으로의 진화

- Result 타입을 사용해 함수의 반환값을 Ok (성공) 또는 Err (오류)로 구조화하기.
    
    ```tsx
    type Result<T, E> = { kind: "ok"; value: T } | { kind: "err"; error: E };
    
    function divide(a: number, b: number): Result<number, string> {
      if (b === 0) return { kind: "err", error: "Division by zero" };
      return { kind: "ok", value: a / b };
    }
    ```
    
    ```tsx
    const result = divide(10, 2);
    if (result.kind === "err") {
      console.error(result.error);
    } else {
      console.log(result.value); *// 5*
    }
    ```
    
- **장점**:
    
    - **타입 강제성**:
        
        - kind를 체크하지 않으면 value나 error에 접근할 수 없습니다. TypeScript가 컴파일 타임에 오류를 잡아줍니다. 반면 handleAsync는 data가 null일 수 있음을 호출자가 기억해야 합니다
        - 런타임 오류 대신 컴파일 타임에 문제 발견 가능.
        - 모든 경우를 처리하도록 강제(배 exhaustive checking).
        - 오류 처리가 함수 시그니처에 명시됨.
        
        ```tsx
        console.log(result.value); *// 오류: 'value'는 'Result'에 없을 수 있음*
        ```
        
    - **함수형 접근**:
        
        - 성공/실패를 기반으로 값을 변환하거나 연쇄 작업을 쉽게 구성할 수 있습니다:
            
            ```tsx
            function mapResult<T, U>(result: Result<T, string>, fn: (v: T) => U): Result<U, string> {
              return result.kind === "ok" ? { kind: "ok", value: fn(result.value) } : result;
            }
            ```