### Zod란 ?

`Zod`는 `TypeScript`에서 사용하는 스**키마 선언 및 유효성 검사 라이브러리**입니다. `Zod`를 사용하면 **데이터 구조를 명확하게 정의**하고, **타입 안전성을 보장**하며, **런타임에서 데이터 유효성을 검사**할 수 있습니다. 즉, **컴파일 시점**과 **런타임** 모두에서 **데이터의 정확성을 높일 수 있는 도구**입니다.

---

### 특징
- **타입 안전성**: `Zod` 스키마는 `TypeScript` 타입을 **자동으로 유추**합니다. 따라서 **추가적인 타입 정의 없이도 데이터의 타입을 보장**할 수 있습니다.
- **쉬운 사용법**: `Zod`는 간결한 API를 제공하여 쉽게 배울 수 있습니다.
- **확장성**: 복잡한 데이터 구조도 손쉽게 정의할 수 있으며, 커스텀 유효성 검사를 추가할 수 있습니다.
- **런타임 유효성 검사**: 데이터를 받을 때 즉시 유효성을 검사하여 오류를 방지할 수 있습니다.

---

### 설치

```bash
npm install zod
```

---

### 사용법

1.  기본 스키마 정의

```typescript
import { z } from 'zod';

const UserSchema = z.object({
  name: z.string(),
  age: z.number().min(18), // 최소 18세 이상이어야 함
  email: z.string().email(), // 이메일 형식이어야 함
});

const userData = {
  name: "John Doe",
  age: 25,
  email: "john.doe@example.com",
};

try {
  UserSchema.parse(userData);
  console.log("유효한 데이터입니다!");
} catch (e) {
  console.error("유효하지 않은 데이터:", e.errors);
}
```

위 코드에서 `UserSchema`는 `name`, `age`, `email` 필드를 가지는 객체를 정의하고 있습니다. 그리고 `parse` 메소드를 사용해 데이터를 검증합니다. 만약 데이터가 스키마와 맞지 않는다면, 에러가 발생하며 `catch` 블록에서 처리할 수 있습니다.

2. 배열 및 중첩된 스키마

```typescript
const PostSchema = z.object({
  title: z.string(),
  content: z.string(),
  tags: z.array(z.string()), // 문자열 배열
  author: UserSchema, // 앞서 정의한 UserSchema 사용
});

const postData = {
  title: "Zod 라이브러리 알아보기",
  content: "Zod는 TypeScript에서 사용하는 데이터 검증 라이브러리입니다...",
  tags: ["typescript", "validation", "zod"],
  author: userData,
};

try {
  PostSchema.parse(postData);
  console.log("유효한 게시물 데이터입니다!");
} catch (e) {
  console.error("유효하지 않은 게시물 데이터:", e.errors);
}
```

이 예제에서는 `PostSchema`에서 `UserSchema`를 중첩하여 사용하고 있습니다. 이를 통해 객체가 서로 어떻게 연결되어 있는지 명확하게 표현할 수 있습니다.

3. 선택적 필드와 기본값

```typescript
const OptionalSchema = z.object({
  name: z.string().optional(), // 선택적 필드
  age: z.number().default(18), // 기본값 18
});

const optionalData = {
  name: "Jane Doe",
};

const result = OptionalSchema.parse(optionalData);
console.log(result); // { name: 'Jane Doe', age: 18 }
```

`optional()` 메소드를 사용해 필드를 선택적으로 만들 수 있으며, `default()` 메소드를 사용해 필드에 기본값을 설정할 수 있습니다.

---

### 고급 기능

1. 커스텀 유효성 검사

```typescript
// 사용자의 나이가 짝수여야 한다는 조건을 추가
const EvenAgeSchema = z.object({
  age: z.number().refine((val) => val % 2 === 0, {
    message: "나이는 짝수여야 합니다.",
  }),
});

try {
  EvenAgeSchema.parse({ age: 21 });
} catch (e) {
  console.error(e.errors); // 나이는 짝수여야 합니다.
}
```

2. Union과 Enum

```typescript
const StatusSchema = z.enum(["active", "inactive", "pending"]);

try {
  StatusSchema.parse("active"); // 유효
  StatusSchema.parse("deleted"); // 오류 발생
} catch (e) {
  console.error(e.errors);
}

const MultiTypeSchema = z.union([z.string(), z.number()]);

try {
  MultiTypeSchema.parse(42); // 유효
  MultiTypeSchema.parse(true); // 오류 발생
} catch (e) {
  console.error(e.errors);
}
```

