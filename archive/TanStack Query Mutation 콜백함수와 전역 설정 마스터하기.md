## Mutation 콜백 함수의 실행 순서 및 예시

### 콜백 함수 우선순위
1. **onMutate**: 뮤테이션 시작 전 실행
   - 낙관적 업데이트 구현
   - 롤백을 위한 컨텍스트 준비

2. **API 요청**: 서버와 통신

3. **onSuccess/onError**: 요청 결과에 따라 실행
   - `onSuccess`: 요청 성공 시
   - `onError`: 요청 실패 시

4. **onSettled**: 항상 마지막에 실행
   - 성공/실패 여부와 무관
   - 최종 정리 작업

### 코드 예시
```typescript
const mutation = useMutation({
  mutationFn: createTodo,
  onMutate: async (newTodo) => {
    await queryClient.cancelQueries({ queryKey: ['todos'] })
    const previousTodos = queryClient.getQueryData(['todos'])
    
    queryClient.setQueryData(['todos'], (old) => [...old, newTodo])
    return { previousTodos }
  },
  onSuccess: (data) => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
  onError: (error, variables, context) => {
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  }
})
```

---
## 글로벌 설정: MutationCache와 QueryClient

### 설정 우선순위
1. **개별 뮤테이션/쿼리 설정** (최우선)
2. **QueryClient 전역 설정** (두 번째)
3. **기본 라이브러리 설정 (MutationCache)** (마지막)

### 글로벌 설정 예시
```typescript
const mutationCache = new MutationCache({
  onError: (error, mutation) => {
    console.error(`Mutation failed: ${mutation.meta?.id}`, error)
  }
})

const queryClient = new QueryClient({
  mutationCache,
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,
      cacheTime: 30 * 60 * 1000,
      refetchOnWindowFocus: false
    },
    mutations: {
      onError: (error) => {
        errorNotification(error)
      }
    }
  }
})
```

## 핵심 포인트
- 콜백 함수는 특정 순서로 실행됨
- 글로벌 설정으로 일관된 에러 처리 가능
- 개별 설정으로 유연성 확보