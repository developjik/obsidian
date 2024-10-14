### Strict Mode란?

`React`의 `Strict Mode`는 **애플리케이션의 잠재적인 문제를 식별하는 데 도움을 주는 개발 모드 전용 도구**입니다. 이는 실제 컴포넌트가 아니며, **자손들에 대한 부가적인 검사와 경고를 활성화**합니다.

---
### 주요 기능

1. **안전하지 않은 생명주기 메서드 식별**: 

예시:
```jsx
class OldComponent extends React.Component {
  componentWillMount() {
    // 이 메서드는 더 이상 권장되지 않습니다.
    console.log("컴포넌트가 마운트됩니다");
  }

  render() {
    return <div>Hello World</div>;
  }
}
```
이 경우, `Strict Mode`는 `componentWillMount`가 안전하지 않은 생명주기 메서드임을 경고합니다.

2. **레거시 문자열 ref 사용에 대한 경고**: 

예시:
```jsx
class OldRefComponent extends React.Component {
  render() {
    return <div ref="myDiv">Hello World</div>;
  }
}
```
이 코드에서 `ref="myDiv"`와 같은 문자열 ref 사용 시 `Strict Mode`가 경고를 표시합니다.

3. **예기치 않은 부작용 검사**: 

예시:
```jsx
function ExampleComponent() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 이 부작용은 마운트와 업데이트 시 두 번 실행됩니다.
    console.log("컴포넌트가 렌더링되었습니다");
  });

  return <div>{count}</div>;
}
```
`Strict Mode`에서는 이 `useEffect`가 개발 모드에서 두 번 실행되어, 부작용의 일관성을 검사합니다.

4. **구 Context API 사용 감지**: 

예시:
```jsx
class OldContextComponent extends React.Component {
  static childContextTypes = {
    color: PropTypes.string
  };

  getChildContext() {
    return { color: "purple" };
  }

  render() {
    return <Button>Click me</Button>;
  }
}
```
이러한 구 `Context API` 사용 시 `Strict Mode`가 경고를 표시합니다.

5. **예기치 않은 부작용 검사를 위한 이중 렌더링**:

예시:
```jsx
function DoubleRenderComponent() {
  console.log("렌더링");
  return <div>Hello World</div>;
}
```

--- 

### 사용 방법

#### React

`Strict Mode`를 사용하려면 애플리케이션의 루트나 특정 부분을 `<React.StrictMode>` 태그로 감싸면 됩니다.

```jsx
import React from 'react';

function App() {
  return (
    <React.StrictMode>
      <div>
        {/* 여기에 애플리케이션 컴포넌트들이 위치합니다 */}
      </div>
    </React.StrictMode>
  );
}

export default App;
```

#### Next.js


1. 프로젝트 루트 디렉토리에 있는 `next.config.js` 파일을 엽니다. 만약 이 파일이 없다면 새로 생성합니다.

2. 다음과 같이 `next.config.js` 파일을 수정하거나 작성합니다:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  // 기타 Next.js 설정...
}

module.exports = nextConfig
```

3. 이 설정을 저장한 후 Next.js 개발 서버를 재시작합니다.

이렇게 하면 전체 Next.js 애플리케이션에 대해 `React Strict Mode`가 활성화됩니다.

---

### 이점

1. **버그 조기 발견**: 잠재적인 문제를 개발 단계에서 미리 발견할 수 있습니다.
2. **최신 모범 사례 준수**: `React`의 최신 권장 사항을 따르도록 유도합니다.
3. **안정성 향상**: 부작용을 줄이고 예측 가능한 애플리케이션 동작을 보장합니다.
4. **미래 대비**: 향후 `React` 버전과의 호환성을 개선합니다.

---
### 주의사항

1. `Strict Mode`는 개발 모드에서만 추가적인 검사를 수행합니다. 프로덕션 빌드에서는 이러한 추가 검사가 실행되지 않습니다.

2. Next.js 13 이상의 버전에서는 기본적으로 `reactStrictMode: true`가 설정되어 있습니다. 만약 이를 비활성화하고 싶다면 `next.config.js`에서 `reactStrictMode: false`로 설정할 수 있습니다.

3. `Strict Mode`를 활성화하면 일부 라이브러리나 레거시 코드에서 경고나 오류가 발생할 수 있습니다. 이는 해당 코드가 `React`의 최신 권장 사항을 따르지 않기 때문입니다. 이러한 경우, 관련 코드를 최신 패턴에 맞게 업데이트하는 것이 좋습니다.

