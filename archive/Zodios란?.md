**Zodios**는 **Zod**와 **Axios**를 결합한 **타입 안전한 API 클라이언트 라이브러리**입니다.

**이를 통해, 컴파일 타임**과 **런타임** 모두에 타입 안전성을 보장하며, 개발자에게 편리한 API 통신 환경을 제공합니다.

<aside> 💡

- **Zod**: TypeScript에서 스키마 유효성 검사를 위한 라이브러리로, 런타임에서 데이터의 유효성을 검증할 수 있습니다.
- **Axios**: 널리 사용되는 HTTP 클라이언트 라이브러리로, 간편한 API 요청을 가능하게 합니다. </aside>

---

## **✨ Zodios의 주요 기능**

**1️⃣ 타입 안전성 보장**

Zodios는 **Zod**를 사용해 API 요청과 응답의 스키마를 정의합니다. 이를 통해 **TypeScript**의 타입 시스템과 **Zod**의 런타임 유효성 검사를 결합하여, **컴파일 타임**과 **런타임** 모두에서 타입 안전성을 보장합니다.

**2️⃣ 자동 완성과 타입 추론**

API 엔드포인트를 정의하면, Zodios는 자동으로 요청과 응답의 타입을 추론합니다. 이는 개발자가 API를 사용할 때 **자동 완성**과 **타입 체크**를 통해 실수를 줄이고 생산성을 높이는 데 큰 도움을 줍니다.

**3️⃣ 간편한 API 정의**

Zodios는 직관적인 방식으로 API 엔드포인트를 정의할 수 있습니다. 각 엔드포인트의 **URL**, **HTTP 메서드**, **요청 및 응답 스키마**를 한 곳에서 관리할 수 있어 코드의 가독성과 유지보수성이 향상됩니다.

**4️⃣ Axios와의 완벽한 호환성**

Zodios는 **Axios**를 기반으로 하기 때문에, Axios의 모든 기능을 그대로 사용할 수 있습니다. **인터셉터**, **요청 취소**, **타임아웃 설정** 등 Axios의 강력한 기능들을 Zodios에서도 활용할 수 있습니다.

---

## **💡 Zodios의 장점**

![_- visual selection (14).svg](attachment:4df04095-b220-4da7-b42c-3b458dc3fda9:_-_visual_selection_(14).svg)

**1️⃣ 타입 안전성 강화**

Zodios는 **Zod**를 통해 런타임 유효성 검사를 제공하며, TypeScript와의 통합을 통해 컴파일 타임에도 타입 안전성을 보장합니다. 이를 통해 잘못된 데이터 형식으로 인한 버그를 사전에 방지할 수 있습니다.

**2️⃣ 개발 생산성 향상**

자동 완성과 타입 추론을 통해 개발자는 API를 사용할 때 실수를 줄이고, 빠르게 개발할 수 있습니다. 또한, API 엔드포인트를 한 곳에서 관리함으로써 코드의 가독성과 유지보수성이 향상됩니다.

**3️⃣ Axios의 강력한 기능 활용**

Zodios는 **Axios**를 기반으로 하기 때문에, Axios의 모든 기능을 그대로 사용할 수 있습니다. 이는 기존에 Axios를 사용하던 개발자들에게 친숙한 환경을 제공합니다.

---

## **🛠️ Zodios 사용법**

**1️⃣ 설치**

```
npm install zodios zod axios
```

**2️⃣ API 정의**

```
import { zodios } from "@zodios/core";
import { z } from "zod";

// Zod 스키마 정의
const userSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
});

// API 엔드포인트 정의
const apiClient = zodios("<https://api.example.com>", [
  {
    method: "get",
    path: "/users/:id",
    response: userSchema,
  },
  {
    method: "post",
    path: "/users",
    parameters: [
      {
        name: "body",
        schema: userSchema.omit({ id: true }),
      },
    ],
    response: userSchema,
  },
]);
```

**3️⃣ API 요청**

```
// 사용자 조회
const user = await apiClient.get("/users/:id", { params: { id: 1 } });
console.log(user);

// 사용자 생성
const newUser = await apiClient.post("/users", {
  name: "John Doe",
  email: "john@example.com",
});
console.log(newUser);
```

---

## zodios/react

**zodios/react**는 **tanstack-query**를 감싸는 강력한 래퍼로, 타입 안전성이 보장된 API 호출과 자동 키 관리, 그리고 향상된 TypeScript 지원을 제공합니다.

---

### zodios/react 설정하기

```tsx
import { Zodios } from "@zodios/core";
import { ZodiosHooks } from "@zodios/react";

const apiClient = new Zodios(baseUrl, [...]); // Zodios API 클라이언트
const apiHooks = new ZodiosHooks("myAPI", apiClient); //ZodiosHooks 인스턴스
```

<aside> 💡

**ZodiosHooks**의 첫 번째 매개변수("myAPI")는 **tanstack-query** 키의 접두사로 사용됩니다.

</aside>

---

## zodios/react 핵심 기능

### **1️⃣ Query** Hooks

- **Basic Query**

```tsx
const {
  data,
  isLoading,
  isError,
  invalidate,
  key
} = apiHooks.useQuery('/users');
```

- **Alias-based Query**

```tsx
// API 설정에 정의된 별칭 사용
const { data: users } = apiHooks.useGetUsers();
```

- **Immutable Query (POST-based queries)**

```tsx
const { data } = apiHooks.useImmutableQuery('/users/search', {
  name: "John"
});
```

### 2️⃣ **Mutation** Hooks

- **Basic Mutation**

```tsx
const { mutate } = apiHooks.useMutation('post', '/users');
```

- **Method-Specific Hooks**

```tsx
// POST
const { mutate: createUser } = apiHooks.usePost("/users");

// PUT
const { mutate: updateUser } = apiHooks.usePut("/users/:id");

// PATCH
const { mutate: patchUser } = apiHooks.usePatch("/users/:id");

// DELETE
const { mutate: deleteUser } = apiHooks.useDelete("/users/:id");
```

### **3️⃣ Infinite Queries**

```tsx
const {
  data: userPages,
  isFetching,
  fetchNextPage
} = apiHooks.useInfiniteQuery(
  "/users",
  {
    queries: { limit: 10 }
  },
  {
    getPageParamList: () => ["page"],
    getNextPageParam: (lastPage, pages) => lastPage.nextPage
      ? {
          queries: {
            page: lastPage.nextPage,
          },
        }
      : undefined
  }
);
```

### 4️⃣ **Cache Management**

- **Key Generation**

```tsx
// 특정 엔드포인트의 키 가져오기
const key = apiHooks.getKeyByPath('get', '/users/:id', {
  params: { id: 1 }
});

// 별칭으로 키 가져오기
const key = apiHooks.getKeyByAlias('getUser', {
  params: { id: 1 }
});
```

- **Cache Invalidation**

```tsx
// invalidate 헬퍼 사용
const { invalidate } = apiHooks.useQuery('/users');
invalidate();

// React Query 클라이언트 사용
const key = apiHooks.getKeyByPath('get', '/users/:id');
queryClient.invalidateQueries(key);
```

---

## ⭐️ Hooks.use[Alias]

Zodios에서는 엔드포인트를 호출할 때 alias를 사용하는 것이 일반적입니다. API 정의 엔드포인트의 `alias` 옵션에서 이를 정의할 수 있습니다.

### Query Alias

Query alias hooks는 react-query의 `QueryResult` 객체를 반환하며, 다음 항목들을 포함합니다:

- 응답 데이터와 모든 react-query 결과 속성들
- 자동 생성된 `key`
- `invalidate` 헬퍼 함수

### Basic Query Alias

```tsx
function use[Alias](
  config?: ZodiosRequestOptions,
  queryOptions: QueryOptions
): QueryResult<Response>;

// 사용 예시
const {
  data: users,
  isLoading,
  isError,
  invalidate,
  key
} = hooks.useGetUsers();
// 위 코드는 hooks.useQuery("/users")와 동일
```

### Immutable Query Alias

immutable 쿼리는 POST 요청에 대한 특별한 형태의 alias입니다.

```tsx
function use[Alias](
  body: Body,
  config?: ZodiosRequestOptions,
  queryOptions: QueryOptions
): QueryResult<Response>;

// 사용 예시
const {
  data: users,
  isLoading,
  isError
} = hooks.useSearchUsers({ name: "John" });
// 위 코드는 hooks.useImmutableQuery("/users/search")와 동일
```

> 주의사항: Immutable query alias는 post 엔드포인트에서만 사용 가능합니다. API 정의 엔드포인트에서 immutable 옵션을 true로 설정해야 useImmutableQuery 훅을 사용할 수 있습니다.

## Mutation Alias

Mutation alias는 `post`, `put`, `patch`, `delete` 엔드포인트에 사용됩니다.

```tsx
function use[Alias](
  config?: ZodiosRequestOptions,
  queryOptions?: QueryOptions
): MutationResult<Response>;

// 사용 예시
const { mutate } = hooks.useCreateUser();
// 위 코드는 usePost("/users") 또는 useMutation("post","/users")와 동일

```

## 실제 사용 예시

### 1️⃣ GET 요청 Alias

```tsx
// API 정의
const endpoints = [{
  method: "get",
  path: "/users",
  alias: "getUsers",
  response: z.array(UserSchema)
}];

// 컴포넌트에서 사용
function UserList() {
  const {
    data: users,
    isLoading,
    invalidate
  } = hooks.useGetUsers();

  if (isLoading) return <div>로딩 중...</div>;

  return (
    <div>
      <button onClick={invalidate}>새로고침</button>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```

### 2️⃣ Immutable POST 요청 Alias

```tsx
// API 정의
const endpoints = [{
  method: "post",
  path: "/users/search",
  alias: "searchUsers",
  immutable: true,
  parameters: [{
    name: "searchParams",
    type: "Body",
    schema: z.object({
      name: z.string()
    })
  }],
  response: z.array(UserSchema)
}];

// 컴포넌트에서 사용
function SearchUsers() {
  const {
    data: users,
    isLoading
  } = hooks.useSearchUsers({
    name: "John"
  });

  if (isLoading) return <div>검색 중...</div>;

  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```

### 3️⃣ Mutation Alias

```tsx
// API 정의
const endpoints = [{
  method: "post",
  path: "/users",
  alias: "createUser",
  parameters: [{
    name: "user",
    type: "Body",
    schema: UserSchema
  }],
  response: UserSchema
}];

// 컴포넌트에서 사용
function CreateUser() {
  const { mutate, isLoading } = hooks.useCreateUser();

  const handleSubmit = (userData) => {
    mutate(userData, {
      onSuccess: () => {
        console.log('사용자 생성 성공');
      }
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* 폼 내용 */}
    </form>
  );
}
```
