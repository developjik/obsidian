### 1. 컨테이너 컴포넌트란?

`컨테이너 컴포넌트(Container Component)`는 애플리케이션의 **비즈니스 로직**을 처리하고, 데이터 소스와 상호작용하며, **프레젠테이션 컴포넌트에 데이터를 전달하는 역할**을 합니다. 즉, 상태 관리, 데이터 페칭, 이벤트 핸들링과 같은 기능을 담당합니다.

**특징:**
- 데이터 접근 및 변형을 처리
- 상태 관리 및 상태 변경 로직 포함
- 프레젠테이션 컴포넌트에 데이터를 전달

### 2. 프레젠테이션 컴포넌트란?

`프레젠테이션 컴포넌트(Presentation Component)`는 **사용자 인터페이스(UI)를 담당**하며, 주로 데이터 표시와 사용자 이벤트에 대한 반응을 처리합니다. **이 컴포넌트는 자체적으로 상태를 관리하지 않고, 필요한 모든 데이터를 props를 통해 전달받습니다**.

**특징:**
- UI 렌더링에 집중
- 스타일링 및 레이아웃 관리
- 상태나 비즈니스 로직을 포함하지 않음
- 부모 컴포넌트로부터 props를 통해 데이터 수신

### 3. 컨테이너 및 프레젠테이션 컴포넌트의 장점

1. **책임 분리**: `UI 렌더링`과 `비즈니스 로직`을 **분리**하여 각 컴포넌트가 **단일 책임 원칙**(Single Responsibility Principle)을 따르도록 합니다.
2. **재사용성 증가**: `프레젠테이션 컴포넌트`는 상태와 비즈니스 로직에 의존하지 않기 때문에 다른 컨텍스트에서 쉽게 **재사용**할 수 있습니다.
3. **테스트 용이성**: `컨테이너 컴포넌트`는 주로 상태 변경 로직을 테스트하고, `프레젠테이션 컴포넌트`는 렌더링 결과를 테스트하는 등 각각의 **테스트를 단순화**할 수 있습니다.
4. **유지보수성 향상**: 역할이 명확하게 분리되므로 **코드 유지보수**가 쉬워집니다.

### 4. 구현 방법

#### 4.1 컨테이너 컴포넌트 예시

```javascript
import React, { useState, useEffect } from 'react';
import UserList from './UserList';

const UserContainer = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/users')
      .then(response => response.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      });
  }, []);

  return <UserList users={users} loading={loading} />;
};

export default UserContainer;
```

#### 4.2 프레젠테이션 컴포넌트 예시

```javascript
import React from 'react';

const UserList = ({ users, loading }) => {
  if (loading) {
    return <div>Loading...</div>;
  }

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

export default UserList;
```

### 5. 결론

`컨테이너 및 프레젠테이션 컴포넌트 패턴`은 **UI와 비즈니스 로직을 분리**하여 보다 **명확**하고 **유지보수하기 쉬운** 애플리케이션을 만들 수 있도록 도와줍니다. 이 패턴을 적용하면 **컴포넌트의 책임이 명확**해지며, **테스트**와 **재사용성**이 향상됩니다. 