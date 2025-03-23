Result 타입은 함수형 프로그래밍의 영향을 받은 패턴으로, 주로 **오류 처리(error handling)** 를 보다 안전하고 명확하게 하기 위해 사용됩니다.

Rust, Haskell, F#, Elm 같은 언어에서 익숙한 개념이며, **에러를 명시적으로 표현**하여 예외(Exception) 기반 오류 처리의 단점을 보완하는 접근 방식입니다.

## **1️⃣ Result 타입이란?**

Result 타입은 일반적으로 두 개의 변형(variants)을 갖는 **이진 합 타입(sum type)** 입니다.

```tsx
type Result<T, E> =
  | { type: "Ok"; value: T }  // 성공 케이스
  | { type: "Err"; error: E } // 실패 케이스
```

즉, 결과가 Ok(value)이면 성공, Err(error)이면 실패를 의미합니다.

이를 통해 함수가 항상 오류를 반환할 가능성을 명시적으로 나타낼 수 있습니다.

---

## **2️⃣ Rust에서의 Result 타입**

Rust에서는 Result<T, E> 타입이 표준 라이브러리에 포함되어 있습니다.

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

사용 예제:

```rust
fn divide(x: f64, y: f64) -> Result<f64, String> {
    if y == 0.0 {
        Err(String::from("Cannot divide by zero"))
    } else {
        Ok(x / y)
    }
}

fn main() {
    match divide(10.0, 2.0) {
        Ok(result) => println!("Result: {}", result),
        Err(error) => println!("Error: {}", error),
    }
}
```

이렇게 하면 **예외가 발생하지 않고 오류를 안전하게 처리**할 수 있습니다.

---

## **3️⃣ Haskell에서의 Either 타입**

Haskell에서는 Result 타입 대신 **Either 타입**을 사용하여 비슷한 기능을 구현합니다.

```haskell
data Either a b = Left a | Right b
```

사용 예제:

```haskell
safeDiv :: Float -> Float -> Either String Float
safeDiv _ 0 = Left "Cannot divide by zero"
safeDiv x y = Right (x / y)

main = case safeDiv 10 2 of
    Left err -> putStrLn ("Error: " ++ err)
    Right result -> print result
```

여기서 Left는 오류, Right는 성공을 의미합니다.

---

## **❌ 예외(Exception) 방식의 문제점**

• 오류가 발생하면 프로그램이 중단될 수 있음.

• 오류가 숨겨져 있어 코드 흐름을 예측하기 어려움.

• try/catch를 강제하지 않으면 오류를 놓칠 가능성이 있음.

```jsx
function divide(x, y) {
    if (y === 0) {
        throw new Error("Cannot divide by zero");
    }
    return x / y;
}

try {
    console.log(divide(10, 0));
} catch (error) {
    console.error(error.message);
}
```

예외를 던지면 try/catch가 없을 경우 프로그램이 비정상 종료될 수도 있습니다.

---

## **✅ Result 방식의 장점**

• 반환값으로 성공/실패를 명확하게 구분.

• 함수가 언제든지 오류를 반환할 수 있음을 **타입 시스템에서 강제**.

• 오류 처리가 **명시적**이고 예측 가능함.

---

## **💡TypeScript에서 Result 구현 예제**

TypeScript에서도 Rust와 유사한 Result 타입을 만들 수 있습니다.

```tsx
type Result<T, E> =
  | { type: "Ok"; value: T }
  | { type: "Err"; error: E };

function divide(x: number, y: number): Result<number, string> {
    if (y === 0) {
        return { type: "Err", error: "Cannot divide by zero" };
    }
    return { type: "Ok", value: x / y };
}

// 사용 예시
const result = divide(10, 0);

if (result.type === "Ok") {
    console.log("Result:", result.value);
} else {
    console.error("Error:", result.error);
}
```

이렇게 하면 try/catch 없이도 안전한 오류 처리가 가능합니다.

---

## 결론

• Result<T, E>는 **예외 없이 오류를 안전하게 처리**하는 패턴.

• **명시적인 오류 처리**를 통해 코드 가독성이 향상됨.

• **함수형 프로그래밍과 궁합이 좋으며**, map, and_then 등을 활용한 체이닝이 가능.

• Rust, Haskell뿐만 아니라 TypeScript, F#, Scala 등에서도 활용 가능.

**즉, Result 패턴은 예외(Exception) 기반 처리의 단점을 보완하며, 함수형 프로그래밍의 영향으로 더 안전하고 예측 가능한 코드를 작성하는 데 유용한 패턴입니다.** 🚀