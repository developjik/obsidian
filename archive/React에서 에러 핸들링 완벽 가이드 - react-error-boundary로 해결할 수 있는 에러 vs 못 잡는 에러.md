React 애플리케이션을 개발하다 보면 예기치 않은 에러가 발생할 수 있습니다. 이러한 에러를 적절히 처리하지 않으면 애플리케이션이 크래시되거나 예상치 못한 동작을 할 수 있습니다.

React에서는 `ErrorBoundary` 개념을 도입하여 컴포넌트 렌더링 중 발생하는 에러를 처리할 수 있도록 지원하지만, **모든 종류의 에러를 잡을 수 있는 것은 아닙니다.** 이번 글에서는 `react-error-boundary` 라이브러리를 활용하여 잡을 수 있는 에러와 그렇지 않은 에러를 정리하고, 각각의 해결 방법을 소개하겠습니다.

---

## ✅ `react-error-boundary`로 잡을 수 있는 에러

### 1️⃣ 렌더링 중 발생하는 에러 (동기적 오류)

`ErrorBoundary`는 **렌더링 중 발생하는 동기적 에러**만 감지할 수 있습니다. 즉, **컴포넌트가 렌더링되는 동안 발생하는 오류**는 `ErrorBoundary`에서 처리할 수 있습니다.

**예제 코드:**

```tsx
import { ErrorBoundary } from "react-error-boundary";

const BuggyComponent = () => {
  throw new Error("렌더링 중 에러 발생!"); // 이 에러는 ErrorBoundary가 잡을 수 있음
  return <div>Hello</div>;
};

const ErrorFallback = ({ error }: { error: Error }) => (
  <div>⚠️ 오류 발생: {error.message}</div>
);

export default function App() {
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback}>
      <BuggyComponent />
    </ErrorBoundary>
  );
}
```

<aside>

🔹 `ErrorBoundary`가 감지할 수 있는 에러 유형

- `throw new Error(...)`를 통해 직접 발생한 에러
- 클래스형 컴포넌트 `render()` 메서드 내부에서 발생한 에러
- 함수형 컴포넌트 렌더링 중 발생한 에러 </aside>

---

## ❌ `react-error-boundary`가 감지하지 못하는 에러

### 1️⃣ `이벤트 핸들러`에서 발생하는 에러

```tsx
const BuggyButton = () => {
  const handleClick = () => {
    throw new Error("이벤트 핸들러에서 에러 발생!"); 
  }; // ❌ ErrorBoundary가 이 에러를 감지하지 못함!

  return <button onClick={handleClick}>클릭</button>;
};
```

---

### 2️⃣ `useEffect` 내부에서 발생하는 에러

```tsx
const BuggyComponent = () => {
  // useEffect는 렌더링 후 실행
  useEffect(() => {
    throw new Error("useEffect 내부에서 에러 발생!");
  }, []); // ❌ ErrorBoundary가 이 에러를 감지하지 못함!

  return <div>Hello</div>;
};

```

---

### 3️⃣ 비동기 코드 (`fetch`, `setTimeout`)

```jsx
const BuggyFetchComponent = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('<https://jsonplaceholder.typicode.com/posts/1>')
      .then((res) => res.json())
      .then((json) => setData(json))
      .catch((err) => {
        throw new Error('💥 API 요청 중 오류 발생!'); 
      }); // ❌ ErrorBoundary가 이 에러를 감지하지 못함!
  }, []);

  return <div>{data?.title}</div>;
```

---

### 4️⃣ `Promise`내부에서 발생한 발생하는 에러

```tsx
const BuggyComponent = () => {
  useEffect(() => {
    Promise.reject(new Error("Promise 내부에서 에러 발생!"));
  }, []);

  return <div>Hello</div>;
};
```

---

## 🔨 `react-error-boundary`가 감지하지 못하는 에러 해결하기

1️⃣ `try/catch`로 직접 감싸기

```jsx
const handleClick = () => {
  try {
    throw new Error("이벤트 핸들러에서 에러 발생!");
  } catch (error) {
    console.error("이벤트 핸들러 에러:", error);
  }
};

const fetchData = async () => {
  try {
    const res = await fetch('/api/data');
    if (!res.ok) throw new Error('데이터 요청 실패');
    const data = await res.json();
    return data;
  } catch (error) {
    console.error('비동기 에러 발생:', error);
  }
};
```

---

### **2️⃣ 전역 에러 처리 (window.onerror, unhandledrejection)**

- **window.onerror: 전역 에러 감지(렌더링 에러 + 이벤트 에러)**
- **window.unhandledrejection: 비동기 에러**

```jsx
useEffect(() => {
  const handleError = (event: ErrorEvent) => {
    console.error('전역 에러 발생:', event.error);
  };

  const handlePromiseRejection = (event: PromiseRejectionEvent) => {
    console.error('Unhandled Promise Rejection:', event.reason);
  };

  window.addEventListener('error', handleError);
  window.addEventListener('unhandledrejection', handlePromiseRejection);

  return () => {
    window.removeEventListener('error', handleError);
    window.removeEventListener('unhandledrejection', handlePromiseRejection);
  };
}, []);
```

### 3️⃣ `react-error-boundary` 의 **`showBoundary` 활용하기**

<aside>

- 👉 showBoundary(error) 를 사용하면, **강제로 ErrorBoundary에게 에러를 전달**할 수 있습니다! </aside>
    
- **1️⃣ 비동기 요청(fetch, axios 등)에서 발생한 에러**
    

```jsx
import { useEffect, useState } from 'react';
import { useErrorBoundary } from 'react-error-boundary';

const BuggyFetchComponent = () => {
  const [data, setData] = useState(null);
  const { showBoundary } = useErrorBoundary(); // ❗ ErrorBoundary에 에러 전달할 함수

  useEffect(() => {
    fetch('<https://jsonplaceholder.typicode.com/invalid-url>') // 존재하지 않는 URL
      .then((res) => {
        if (!res.ok) throw new Error('💥 API 응답 오류!');
        return res.json();
      })
      .then((json) => setData(json))
      .catch((err) => showBoundary(err)); // ✅ 강제로 ErrorBoundary에서 감지하도록 함
  }, []);

  return <div>{data?.title}</div>;
};
```

- **2️⃣ 이벤트 핸들러에서 발생한 에러**

```jsx
import { useErrorBoundary } from 'react-error-boundary';

const BuggyButton = () => {
  const { showBoundary } = useErrorBoundary();

  const handleClick = () => {
    try {
      throw new Error('💥 버튼 클릭 에러!');
    } catch (err) {
      showBoundary(err); // ✅ ErrorBoundary가 감지하도록 강제 전달
    }
  };

  return <button onClick={handleClick}>클릭하면 에러 발생</button>;
};
```

- **3️⃣ setTimeout 내에서 발생한 에러**

```jsx
import { useEffect } from 'react';
import { useErrorBoundary } from 'react-error-boundary';

const BuggyTimeoutComponent = () => {
  const { showBoundary } = useErrorBoundary();

  useEffect(() => {
    setTimeout(() => {
      try {
        throw new Error('💥 타임아웃 에러 발생!');
      } catch (err) {
        showBoundary(err); // ✅ setTimeout 내부의 에러도 감지 가능!
      }
    }, 1000);
  }, []);

  return <div>🚀 1초 후 에러 발생</div>;
};
```

<aside>

💡 `Tanstack Query v5` 에서 비동기 오류 처리하기

- `throwOnError`: **true** or **(error, query) => Error** 함수

`throwOnError`가 **true** 시, 발생하는 모든 에러가 자동으로 가장 가까운 ErrorBoundary로 전파됩니다.

즉, `throwOnError` 설정시, 서버 요청에 대한 Error 처리시 try catch or showBoundary등의 처리하지 않아도 됩니다.

(`Tanstack Query v3,v4` 에서는 `useErrorBoundary` 변수로 되어있습니다.)

</aside>

---

## 📌 정리

### **✅** `react-error-boundary`가 잡을 수 있는 에러 vs 못 잡는 에러

|발생 위치|잡을 수 있음?|해결 방법|
|---|---|---|
|렌더링 중 (`throw new Error`)|✅ 가능|`react-error-boundary` 사용|
|이벤트 핸들러 (`onClick` 등)|❌ 불가능|`try/catch`로 감싸기|
|`useEffect` 내부|❌ 불가능|`try/catch`로 감싸기|
|비동기 코드 (`fetch`, `setTimeout`)|❌ 불가능|`try/catch`, `onError` 처리|
|Promise 내부|❌ 불가능|`window.onunhandledrejection` 처리|

### **✅ React 에러 핸들링**

|**상황**|**해결 방법**|
|---|---|
|동기 렌더링 중 에러|react-error-boundary 사용|
|비동기 요청 (fetch, useQuery)|try/catch, onError 핸들링|
|이벤트 핸들러 에러|try/catch 내부 처리|
|전역 에러 감지|window.onerror, unhandledrejection|

---

## 🎯 결론

`react-error-boundary`는 **렌더링 중 동기적인 에러**만 감지할 수 있으며, 이벤트 핸들러나 비동기 코드에서 발생하는 에러는 잡을 수 없습니다.

따라서, **비동기 에러나 이벤트 핸들러 에러는 `try/catch` 또는 전역 핸들러를 활용하여 직접 처리해야 합니다.**