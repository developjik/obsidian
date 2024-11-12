`React` 애플리케이션을 개발하다 보면 다양한 `HTTP` 에러 상황을 마주치게 됩니다. 사용자에게 적절한 에러 메시지를 보여주고, 복구 옵션을 제공하는 것은 좋은 사용자 경험을 위해 매우 중요합니다. 이번 글에서는 `React` `Error Boundary`를 활용하여 `HTTP` 에러를 체계적으로 처리하는 방법을 알아보겠습니다.

## Error Boundary란?

`Error Boundary`는 `React` 16에서 도입된 기능으로, **하위 컴포넌트 트리에서 발생하는 JavaScript 에러를 캐치하고, 에러 UI를 표시할 수 있게 해주는 특별한 컴포넌트**입니다. `HTTP` 에러 처리에 `Error Boundary`를 활용하면 애플리케이션 전반에 걸쳐 일관된 에러 처리 로직을 구현할 수 있습니다.

---
## 구현

### 1. HTTP 에러 판별

먼저 받은 에러가 `HTTP` 에러인지 확인하는 유틸리티 함수를 구현합니다:

```typescript
const isHttpError = (error: unknown): boolean => {
  return error instanceof AxiosError && Boolean(error.response?.status);
};
```

이 함수는 에러 객체가 `Axios`에서 발생한 에러인지, 그리고 `HTTP` 상태 코드가 있는지 확인합니다.

### 2. 에러 메시지 매핑

각 `HTTP` 상태 코드에 따라 적절한 사용자 친화적인 메시지를 반환하는 함수를 구현합니다:

```typescript
const getErrorMessage = (status?: number, message?: string) => {
  if (message) return message;  // 서버 메시지가 존재하는 경우 우선 처리

  switch (status) {
    case 400: return "잘못된 요청입니다";
    case 403: return "접근 권한이 없습니다";
    case 404: return "요청한 리소스를 찾을 수 없습니다";
    // ... 기타 상태 코드
    default: return status && status >= 500
      ? "서버 오류가 발생했습니다"
      : "요청 처리 중 오류가 발생했습니다";
  }
};
```

### 3. Fallback 컴포넌트

에러가 발생했을 때 보여줄 UI 컴포넌트를 구현합니다:

```typescript
const HttpErrorFallback = ({ error, resetErrorBoundary }: FallbackProps) => {
  const router = useNextRouter();
  const queryClient = useQueryClient();

  if (!(error instanceof AxiosError)) {
    throw error;  // HTTP 에러가 아니면 상위로 전파
  }

  const handleRetry = () => {
    queryClient.clear();
    resetErrorBoundary();
  };

  return (
    <div className="flex min-h-screen flex-col items-center justify-center p-4">
      <h2 className="text-2xl font-bold">오류가 발생했습니다</h2>
      <p className="mt-2 text-gray-600">
        {getErrorMessage(error.response?.status, error.response?.data?.message)}
      </p>
      <div className="mt-4 flex gap-2">
        <Button onClick={handleRetry}>다시 시도</Button>
        <Button onClick={() => router.push("/")}>홈으로</Button>
      </div>
    </div>
  );
};
```

이 컴포넌트는:
- 사용자 친화적인 에러 메시지를 표시
- "다시 시도" 옵션을 제공하여 캐시를 초기화하고 재시도
- "홈으로" 버튼으로 안전한 상태로 복귀 가능

### 4. Error Boundary 구성

마지막으로 HOC 패턴을 사용하여 Error Boundary를 구성합니다:

```typescript
export const HttpErrorBoundary = withErrorBoundary(
  HttpErrorBoundaryBase,
  ErrorBoundary,
  HttpErrorFallback,
  {
    onError: (error: Error, info: React.ErrorInfo) => {
      if (!isHttpError(error)) {
        throw error;
      }
      // 에러 로깅
      if (error instanceof AxiosError) {
        console.error("HTTP Error:", {
          status: error.response?.status,
          message: error.response?.data?.message,
          error,
          info,
        });
      }
    },
  },
);
```

---
## 사용 방법

`HttpErrorBoundary`는 다음과 같이 사용할 수 있습니다:

```tsx
function App() {
  return (
    <HttpErrorBoundary>
      <YourComponent />
    </HttpErrorBoundary>
  );
}
```

---
## 장점

1. **중앙화된 에러 처리**: 애플리케이션 전체의 HTTP 에러를 일관되게 처리
2. **사용자 경험 향상**: 친숙한 메시지와 복구 옵션 제공
3. **타입 안전성**: TypeScript를 활용한 타입 체크
4. **유연성**: 서버의 커스텀 에러 메시지 지원
5. **디버깅 용이성**: 체계적인 에러 로깅