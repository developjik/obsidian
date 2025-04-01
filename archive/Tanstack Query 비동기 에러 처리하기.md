아래의 옵션이 기본값으로 설정됬다고 가정합니다.

- [ ] 아래 내용들은 실제 코드를 작성해서 검증이 필요합니다.

<aside>

💡 `Tanstack Query v5` 에서 비동기 오류 처리하기

- `throwOnError`: **true** or **(error, query) => Error** 함수

`throwOnError`가 **true** 시, 발생하는 모든 에러가 자동으로 가장 가까운 ErrorBoundary로 전파됩니다.

즉, `throwOnError` 설정시, 서버 요청에 대한 Error 처리시 try catch or showBoundary등의 처리하지 않아도 됩니다.

(`Tanstack Query v4` 에서는 `useErrorBoundary` 변수로 되어있습니다.)

</aside>

## 일반적인 에러 처리

```
<ErrorBoundary fallback={<div>에러 발생!</div>}>
  <Component />
</ErrorBoundary>
```

- Error 를 ErrorBoundary 에서 catch 하지만, 화면이 rerender 되고, 기존 state 들이 초기화됨

---

## `Tanstack Query v5` 기준 Error 처리 가이드

- 아래의 처리방식을 통한 `reset`은 오직 `Tanstack Query`의 상태만 초기화하고, Component의 `local state(useState)`는 초기화되지 않습니다.

### 1️⃣ QueryErrorResetBoundary

```jsx
<QueryErrorResetBoundary> // Query 에러 발생 시 해당 Query의 상태를 초기화하고 다시 시도할 수 있는 기능 제공
  {({ reset }) => (
    <ErrorBoundary
      onReset={reset}
      fallbackRender={({ resetErrorBoundary }) => (
        <div>
          쿼리 에러 발생!
          <button onClick={resetErrorBoundary}>다시 시도</button>
        </div>
      )}
    >
      <Component />
    </ErrorBoundary>
  )}
</QueryErrorResetBoundary>

```

### 2️⃣ useQueryErrorResetBoundary

```jsx
const App = () => {
  const { reset } = useQueryErrorResetBoundary()
  return (
    <ErrorBoundary
      onReset={reset}
      fallbackRender={({ resetErrorBoundary }) => (
        <div>
          에러 발생!
          <button onClick={resetErrorBoundary}>다시 시도</button>
        </div>
      )}
    >
      <Component />
    </ErrorBoundary>
  )
}
```

---

## 권장되는 ErrorBoundary Pattern

### 1️⃣ 일반적인 JS error + tanstack query error 처리

```jsx
const App = () => (
  // 일반적인 JS 에러를 위한 ErrorBoundary
  <ErrorBoundary 
    fallback={<div>애플리케이션 에러가 발생했습니다</div>}
  >
    {/* Query 관련 에러를 위한 ErrorBoundary */}
    <QueryErrorResetBoundary>
      {({ reset }) => (
        <ErrorBoundary
          onReset={reset}
          fallbackRender={({ resetErrorBoundary }) => (
            <div>
              데이터 로딩 중 에러가 발생했습니다
              <button onClick={resetErrorBoundary}>다시 시도</button>
            </div>
          )}
        >
          <MyComponent />
        </ErrorBoundary>
      )}
    </QueryErrorResetBoundary>
  </ErrorBoundary>
)
```

```jsx
const MyComponent = () => {
  // 🟢 유지됨 - reset 후에도 이 값은 그대로
  const [localState, setLocalState] = useState("initial");

  // 🔄 재실행됨 - reset 시 query가 다시 실행됨
  const { data } = useQuery({
    queryKey: ['myData'],
    queryFn: () => {
      if (Math.random() > 0.5) throw new Error('Query Failed');
      return 'success';
    },
    throwOnError: true
  });

  return (
    <div>
      <p>Local State: {localState}</p>  {/* 값이 유지됨 */}
      <p>Query Data: {data}</p>         {/* 쿼리 재실행으로 새로운 값 */}
    </div>
  );
};
```

- 외부 ErrorBoundary: 런타임 에러, render 오류 등 일반적인 JS 에러 처리
    - 일반 에러는 더 심각한 문제일 수 있으므로 별도 처리 필요
- 내부 QueryErrorResetBoundary: 데이터 fetching 관련 에러만 특별히 처리
    - 쿼리 에러는 보통 재시도가 가능하므로 reset 기능 제공

### 2️⃣ event error 처리

```jsx
import { useCallback } from "react";

/**
 * 애플리케이션 전반의 에러를 일관되게 처리하기 위한 커스텀 훅
 * @param {Function} [customHandler] - 선택적으로 제공할 수 있는 사용자 정의 에러 핸들러
 * @returns {Function} 에러 처리가 포함된 래퍼 함수
 */
export const useErrorHandler = () => {
  return useCallback(
    (fn) => {
      return async (...args) => {
        try {
          return await fn(...args);
        } catch (error) {
	        console.error(error)
	        //추가 공통 Error 처리 로직
        }
      };
    },
    [],
  );
};
```

```jsx
const MyComponent = () => {
  const handleError = useErrorHandler();

  // 이벤트 핸들러에서 에러 처리
  const handleClick = handleError(async () => {
   throw new Error('이벤트 에러')
    await submitData();
  });

  return (
    <button onClick={handleClick}>
      제출
    </button>
  );
};
```