---
tags:
  - react
date: 2024-08-01
---

###  1. HOC(Higher-Order Component) 란 무엇인가?

`HOC(Higher-Order Component)`는 `React`에서 사용되는 디자인 패턴으로, **컴포넌트를 입력으로 받아 새로운 컴포넌트를 반환하는 함수**입니다. 이는 비슷한 로직을 **여러 컴포넌트에서 재사용**할 수 있도록 도와줍니다.

`HOC`는 **입력으로 받은 컴포넌트를 장식**하거나 **추가적인 데이터를 제공**하거나 **기능을 확장**하는 데 사용됩니다. `HOC`를 통해 컴포넌트의 상태, 라이프사이클 메서드, 리액트 콘텍스트 등을 쉽게 공유할 수 있습니다.

### 2. HOC(Higher-Order Component) 사용 예시

#### 2.1. 권한 검사 (Authorization)

`HOC`를 사용하여 사용자가 특정 권한을 가지고 있는지 확인할 수 있습니다. 이 방법으로 권한 검사를 중앙화할 수 있습니다.

```javascript
const withAuthorization = (WrappedComponent) => {
  return (props) => {
    if (!props.isAuthenticated) {
      // 인증되지 않은 사용자라면 리다이렉트
      return <div>Access Denied. Please login.</div>;
    }
    return <WrappedComponent {...props} />;
  };
};

// 사용 예시
const ProtectedComponent = withAuthorization(SomeComponent);
```

#### 2.2. 로딩 상태 관리 (Loading State Management)

데이터 로딩 중에 로딩 스피너를 보여주는 HOC를 만들어보겠습니다.

```javascript
const withLoading = (WrappedComponent) => {
  return ({ isLoading, ...otherProps }) => {
    if (isLoading) {
      return <div>Loading...</div>;
    }
    return <WrappedComponent {...otherProps} />;
  };
};

// 사용 예시
const DataComponent = withLoading(SomeComponent);
```

#### 2.3. 데이터 페칭 (Data Fetching)

서버에서 데이터를 가져오는 로직을 HOC로 추상화할 수 있습니다.

```javascript
const withDataFetching = (WrappedComponent, dataSource) => {
  return (props) => {
    const [data, setData] = React.useState(null);
    const [isLoading, setIsLoading] = React.useState(true);

    React.useEffect(() => {
      fetch(dataSource)
        .then(response => response.json())
        .then(data => {
          setData(data);
          setIsLoading(false);
        });
    }, [dataSource]);

    return <WrappedComponent data={data} isLoading={isLoading} {...props} />;
  };
};

// 사용 예시
const DataComponent = withDataFetching(SomeComponent, 'https://api.example.com/data');
```

### 3. HOC(Higher-Order Component)의 장단점

#### 3. 1. 장점

1. **코드 재사용성**: `HOC`를 사용하면 같은 로직을 **여러 컴포넌트에서 쉽게 재사용**할 수 있습니다.
2. **추상화**: **복잡한 로직을 `HOC`로 추상화**하여 컴포넌트 자체의 코드를 단순하게 유지할 수 있습니다.
3. **유지보수성**: **중앙에서 로직을 관리**할 수 있어 **유지보수와 수정이 간편**합니다.

#### 3.2. 단점

1. **복잡성 증가**: `HOC`를 과도하게 사용하면 코드가 복잡해질 수 있습니다.
2. **디버깅 어려움**: 여러 `HOC`가 적용된 컴포넌트는 디버깅하기 어려울 수 있습니다.
3. **Props 전달 문제**: `HOC`를 사용할 때 원래 컴포넌트의 props가 누락되거나 올바르게 전달되지 않을 수 있습니다.

### 4. 고차 컴포넌트의 구현

```javascript
const higherOrderComponent = (WrappedComponent) => {
  return (props) => {
    // 여기서 필요한 추가적인 로직을 처리할 수 있습니다.
    return <WrappedComponent {...props} />;
  };
};

// 사용 예시
const EnhancedComponent = higherOrderComponent(SomeComponent);
```

이 예시에서는 `higherOrderComponent` 함수가 `WrappedComponent`라는 컴포넌트를 인자로 받아, 추가적인 로직을 적용한 새로운 컴포넌트를 반환합니다. 반환된 컴포넌트는 원래 컴포넌트와 동일한 props를 전달받습니다.

## 5. 결론

`HOC(Higher-Order Component)`는 **React에서 로직을 재사용하고 코드의 유지보수성을 높이는 데** 매우 유용한 패턴입니다. 하지만 **과도한 사용은 코드의 복잡성을 증가**시킬 수 있으므로, 적절한 경우에 사용하는 것이 중요합니다. HOC를 올바르게 사용하면 **컴포넌트의 책임을 명확히** 하고, **코드의 가독성과 유지보수성을 향상**시킬 수 있습니다.