안녕하세요! 오늘은 `Next.js` 프로젝트에서 `JWT(JSON Web Token)` 인증을 구현하는 방법에 대해 알아보겠습니다. 특히 `React Query`와 `Zustand`를 활용하여 더 효율적이고 관리하기 쉬운 인증 시스템을 만드는 과정을 단계별로 살펴보겠습니다.

## 목차
1. 소개
2. 기본 설정
3. JWT 인증 구현
4. React Query와 Zustand 통합
5. Protected Routes 구현
6. 결론

## 1. 소개

`JWT`는 웹 애플리케이션에서 널리 사용되는 **인증 방식**입니다. `React Query`는 **서버 상태 관리**를, `Zustand`는 **클라이언트 상태 관리**를 효율적으로 할 수 있게 해주는 라이브러리입니다. 이들을 `Next.js`와 함께 사용하면 강력하고 유지보수가 쉬운 인증 시스템을 구축할 수 있습니다.

## 2. 기본 설정

먼저, 필요한 패키지를 설치합니다:

```bash
npm install axios react-query zustand
```

## 3. JWT 인증 구현

### 타입 정의

```typescript
// types/auth.ts
export interface User {
  id: string;
  name: string;
  email: string;
}

export interface AuthTokens {
  accessToken: string;
  refreshToken: string;
}
```

### 토큰 관리 유틸리티

```typescript
// utils/auth.ts
import { AuthTokens } from '../types/auth';

export const setAuthTokens = (tokens: AuthTokens) => {
  if (typeof window !== 'undefined') {
    sessionStorage.setItem('accessToken', tokens.accessToken);
    sessionStorage.setItem('refreshToken', tokens.refreshToken);
  }
};

export const getAccessToken = (): string | null => {
  if (typeof window !== 'undefined') {
    return sessionStorage.getItem('accessToken');
  }
  return null;
};

// getRefreshToken과 clearAuthTokens 함수도 유사하게 구현
```

### API 설정

```typescript
// api/authApi.ts
import axios from 'axios';
import { AuthTokens, User } from '../types/auth';
import { setAuthTokens, getAccessToken, getRefreshToken, clearAuthTokens } from '../utils/auth';

const API_URL = 'https://your-api-url.com';

const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// 요청 인터셉터 설정
api.interceptors.request.use((config) => {
  const token = getAccessToken();
  if (token) {
    config.headers['Authorization'] = `Bearer ${token}`;
  }
  return config;
});

// 응답 인터셉터 설정 (토큰 갱신 로직 포함)
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    // 토큰 갱신 로직 구현
  }
);

export const loginApi = async (email: string, password: string): Promise<AuthTokens> => {
  const response = await api.post<AuthTokens>('/login', { email, password });
  setAuthTokens(response.data);
  return response.data;
};

// logoutApi와 getCurrentUserApi 함수도 구현
```

## 4. React Query와 Zustand 통합

### Zustand 스토어 생성

```typescript
// store/authStore.ts
import create from 'zustand';
import { User } from '../types/auth';

interface AuthState {
  user: User | null;
  setUser: (user: User | null) => void;
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
}));
```

### React Query와 Zustand를 사용한 인증 훅

```typescript
// hooks/useAuth.ts
import { useQuery, useMutation, useQueryClient } from 'react-query';
import { useRouter } from 'next/router';
import { loginApi, logoutApi, getCurrentUserApi } from '../api/authApi';
import { useAuthStore } from '../store/authStore';

export const useAuth = () => {
  const queryClient = useQueryClient();
  const router = useRouter();
  const { user, setUser } = useAuthStore();

  const { isLoading: isLoadingUser } = useQuery('currentUser', getCurrentUserApi, {
    onSuccess: (data) => setUser(data),
    onError: () => setUser(null),
    retry: false,
  });

  const loginMutation = useMutation(
    ({ email, password }: { email: string; password: string }) => loginApi(email, password),
    {
      onSuccess: async () => {
        await queryClient.invalidateQueries('currentUser');
        router.push('/dashboard');
      },
    }
  );

  const logoutMutation = useMutation(logoutApi, {
    onSuccess: () => {
      setUser(null);
      queryClient.clear();
      router.push('/login');
    },
  });

  return {
    user,
    isLoadingUser,
    login: loginMutation.mutate,
    isLoggingIn: loginMutation.isLoading,
    logout: logoutMutation.mutate,
    isLoggingOut: logoutMutation.isLoading,
  };
};
```

## 5. Protected Routes 구현

인증이 필요한 페이지를 보호하기 위해 Protected Route 패턴을 구현합니다.

```typescript
// components/ProtectedRoute.tsx
import { useEffect } from 'react';
import { useRouter } from 'next/router';
import { useAuth } from '../hooks/useAuth';

interface ProtectedRouteProps {
  children: React.ReactNode;
}

export const ProtectedRoute: React.FC<ProtectedRouteProps> = ({ children }) => {
  const { user, isLoadingUser } = useAuth();
  const router = useRouter();

  useEffect(() => {
    if (!isLoadingUser && !user) {
      router.push('/login');
    }
  }, [user, isLoadingUser, router]);

  if (isLoadingUser) {
    return <div>Loading...</div>;
  }

  if (!user) {
    return null;
  }

  return <>{children}</>;
};
```

그리고 `_app.tsx`를 수정하여 Protected Route를 적용합니다:

```typescript
// pages/_app.tsx
import type { AppProps } from 'next/app';
import { QueryClient, QueryClientProvider } from 'react-query';
import { ProtectedRoute } from '../components/ProtectedRoute';

const queryClient = new QueryClient();

function MyApp({ Component, pageProps }: AppProps) {
  const isProtectedRoute = pageProps.protected;

  return (
    <QueryClientProvider client={queryClient}>
      {isProtectedRoute ? (
        <ProtectedRoute>
          <Component {...pageProps} />
        </ProtectedRoute>
      ) : (
        <Component {...pageProps} />
      )}
    </QueryClientProvider>
  );
}

export default MyApp;
```

이제 보호가 필요한 페이지에서는 다음과 같이 설정할 수 있습니다:

```typescript
// pages/dashboard.tsx
import { useAuth } from '../hooks/useAuth';

const DashboardPage = () => {
  const { user, logout } = useAuth();

  return (
    <div>
      <h1>Welcome, {user?.name}!</h1>
      <button onClick={() => logout()}>Logout</button>
    </div>
  );
};

export default DashboardPage;

export const getStaticProps = () => {
  return {
    props: {
      protected: true
    }
  };
};
```

## 6. 결론

이렇게 Next.js에서 JWT 인증을 구현하고, React Query와 Zustand를 활용하여 상태 관리를 효율적으로 할 수 있습니다. 이 방식의 주요 이점은 다음과 같습니다:

1. 코드 중복 감소: 인증 로직이 중앙 집중화되어 있습니다.
2. 효율적인 상태 관리: React Query로 서버 상태를, Zustand로 클라이언트 상태를 관리합니다.
3. 타입 안정성: TypeScript를 사용하여 타입 관련 오류를 줄입니다.
4. 확장성: 새로운 보호된 라우트를 쉽게 추가할 수 있습니다.

이 구조를 기반으로 프로젝트의 요구사항에 맞게 추가적인 기능을 구현할 수 있습니다. 예를 들어, 권한 관리, 소셜 로그인 등을 추가할 수 있겠죠.

JWT 인증은 강력하지만, 보안에 주의를 기울여야 합니다. RefreshToken은 안전하게 저장해야 하며, XSS와 CSRF 공격에 대비해야 합니다.

이 글이 여러분의 Next.js 프로젝트에서 JWT 인증을 구현하는 데 도움이 되었기를 바랍니다. 질문이나 의견이 있다면 언제든 댓글로 남겨주세요!

Happy coding!