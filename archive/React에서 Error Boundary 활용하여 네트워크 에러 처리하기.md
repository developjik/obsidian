
웹 애플리케이션을 개발하다 보면 네트워크 오류는 피할 수 없는 문제입니다. 특히 오프라인 상태나 불안정한 네트워크 환경에서는 더욱 그렇죠. 오늘은 `React`의 `Error Boundary`를 활용하여 **네트워크 에러**를 우아하게 처리하는 방법을 알아보겠습니다. (해당 글을 `axios`를 사용한다고 가정합니다.)

## Error Boundary란?

`Error Boundary`는 `React` 16에서 도입된 기능으로, **하위 컴포넌트 트리에서 발생하는 JavaScript 에러를 캐치하고, 에러 UI를 표시할 수 있게 해주는 컴포넌트**입니다. 이를 활용하면 **애플리케이션의 전체적인 에러 처리 로직을 중앙화**할 수 있습니다.

## 네트워크 에러 처리를 위한 커스텀 Error Boundary 구현하기

### 1. 네트워크 에러 판별하기

먼저 네트워크 에러를 식별하는 함수를 만들어보겠습니다:

```typescript
const isNetworkError = (error: unknown): boolean => {
  return error instanceof AxiosError && !error.response;
};
```

이 함수는 두 가지 조건을 확인합니다:
- `error`가 `Axios`에서 발생한 에러인지
- `response`가 없는 에러인지 (서버 응답 자체를 받지 못한 경우)

### 2. Fallback 컴포넌트 구현

에러가 발생했을 때 보여줄 `UI`를 구현합니다:

```typescript
const NetworkErrorFallback = ({ error, resetErrorBoundary }: FallbackProps) => {
  if (!isNetworkError(error)) {
    throw error;
  }

  return (
    <div className="flex min-h-screen flex-col items-center justify-center p-4">
      <h2 className="text-2xl font-bold">네트워크 오류</h2>
      <p className="mt-2 text-gray-600">인터넷 연결을 확인해주세요.</p>
      <Button className="mt-4" onClick={resetErrorBoundary}>
        다시 시도
      </Button>
    </div>
  );
};
```

이 컴포넌트의 특징:
- **네트워크 에러가 아닌 경우 상위로 에러를 전파**
- 사용자 친화적인 에러 메시지 표시
- 재시도 기능 제공

### 3. Error Boundary 구성하기

HOC 패턴을 활용하여 재사용 가능한 `Error Boundary`를 만듭니다:

```typescript
import React from "react";

import { ErrorBoundary, FallbackProps } from "react-error-boundary";


type ErrorBoundaryOptions = {
	onError?: (error: Error, info: React.ErrorInfo) => void;
	onReset?: () => void;
};

function withErrorBoundary<P extends object>(
	WrappedComponent: React.ComponentType<P>,
	ErrorBoundaryComponent: typeof ErrorBoundary,
	FallbackComponent: React.ComponentType<FallbackProps>,
	options?: ErrorBoundaryOptions,
) {
	return function WithErrorBoundaryWrapper(props: P) {
		return (
			<ErrorBoundaryComponent FallbackComponent={FallbackComponent} {...options}>
				<WrappedComponent {...props} />
			</ErrorBoundaryComponent>
		);
	};
}

export { withErrorBoundary };

```


```typescript
import { Button } from "@/components";
import { AxiosError } from "axios";
import { ErrorBoundary, FallbackProps } from "react-error-boundary";
import { withErrorBoundary } from "./with-error-boundary";

// Network Error 판별 함수

const isNetworkError = (error: unknown): boolean => {
	return error instanceof AxiosError && !error.response;
};

const NetworkErrorFallback = ({ error, resetErrorBoundary }: FallbackProps) => {
  if (!isNetworkError(error)) {
    throw error;
  }

  return (
    <div className="flex min-h-screen flex-col items-center justify-center p-4">
      <h2 className="text-2xl font-bold">네트워크 오류</h2>
      <p className="mt-2 text-gray-600">인터넷 연결을 확인해주세요.</p>
      <Button className="mt-4" onClick={resetErrorBoundary}>
        다시 시도
      </Button>
    </div>
  );
};


const NetworkErrorBoundaryBase = ({ children }: { children: React.ReactNode }) => {
	return <>{children}</>;
};

const NetworkErrorBoundary = withErrorBoundary(
  NetworkErrorBoundaryBase,
  ErrorBoundary,
  NetworkErrorFallback,
  {
    onError: (error: Error, info: React.ErrorInfo) => {
      if (!isNetworkError(error)) {
        throw error;
      }
      console.error("Network Error:", {
        error,
        info,
        online: typeof window !== "undefined" ? window.navigator.onLine : null,
      });
    },
  },
);
```

주요 특징:
- 네트워크 에러만 처리하고 다른 에러는 상위로 전파
- 에러 발생 시 온라인 상태 등 디버깅에 유용한 정보 로깅
- SSR 환경 고려 (window 객체 체크)

## 사용 방법

네트워크 요청이 발생하는 컴포넌트를 NetworkErrorBoundary로 감싸기만 하면 됩니다:

```typescript
function App() {
  return (
    <NetworkErrorBoundary>
      <DataFetchingComponent />
    </NetworkErrorBoundary>
  );
}
```

## 장점

1. **관심사의 분리**: 에러 처리 로직을 별도의 컴포넌트로 분리하여 코드 구조가 깔끔해집니다.
2. **재사용성**: 여러 컴포넌트에서 동일한 에러 처리 로직을 재사용할 수 있습니다.
3. **사용자 경험 향상**: 네트워크 오류 발생 시 사용자에게 명확한 피드백을 제공합니다.
4. **디버깅 용이성**: 에러 발생 시 관련 정보를 체계적으로 수집할 수 있습니다.

## 주의사항

1. 이벤트 핸들러 내부에서 발생하는 에러는 Error Boundary가 잡지 못합니다.
2. 비동기 작업에서 발생하는 에러는 try-catch로 직접 처리하고 Error Boundary로 전파해야 합니다.
3. **네트워크 에러만 처리하고 다른 종류의 에러는 상위로 전파하도록 설계해야 합니다.**

