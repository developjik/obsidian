## 소개
`Next.js`에서 **라우팅**을 다룰 때 발생할 수 있는 문제점들을 해결하고, 더 안정적인 라우팅 처리를 위한 커스텀 훅을 소개합니다.

---
## 왜 useNextRouter가 필요한가?

기존 `Next.js`의 `useRouter`를 사용할 때의 일반적인 패턴입니다:
```typescript
function ProductPage() {
  const router = useRouter();
  const productId = router.query.id;

  if (!productId) {
    return <div>로딩중...</div>;
  }

  return <div>상품 ID: {productId}</div>;
}
```

이러한 패턴의 문제점:
- 매번 로딩 상태 체크 필요
- `undefined` 처리를 위한 부가적인 코드
- 타입 안정성 부족
---
## useNextRouter 구현

```typescript
const useNextRouter = (options: Options = { suspense: false }) => {
  const router = useRouter();

  if (options.suspense && !router.isReady) {
    throw waitForRouterReady();
  }

  return router;
};
```

---
## Suspense를 활용한 개선된 사용법

```typescript
function ProductPage() {
  const router = useNextRouter({ suspense: true });
  const productId = router.query.id; // 항상 값이 존재함을 보장

  return <div>상품 ID: {productId}</div>;
}
```

### 장점
1. **간결한 코드**
   - 로딩 상태 체크 불필요
   - 조건부 렌더링 제거

2. **타입 안정성**
   - 라우터가 준비된 상태를 보장
   - undefined 값 걱정 없음

3. **React Suspense 통합**
   - 선언적인 로딩 상태 처리
   - 일관된 로딩 UI 관리

---
## 실제 사용 예제

### 1. 기본적인 사용
```typescript
function BasicPage() {
  const router = useNextRouter();
  // 일반적인 useRouter처럼 사용
}
```

### 2. Suspense와 함께 사용
```typescript
function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <ProductPage />
    </Suspense>
  );
}

function ProductPage() {
  const router = useNextRouter({ suspense: true });
  const productId = router.query.id;
  
  return <div>상품 ID: {productId}</div>;
}
```
---
## 언제 사용하면 좋을까?

1. 쿼리 파라미터에 의존적인 데이터 페칭이 필요할 때
2. 라우터 준비 상태에 따른 안정적인 렌더링이 필요할 때
3. React Suspense를 활용한 로딩 처리를 구현하고 싶을 때