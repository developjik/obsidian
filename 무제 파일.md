`JavaScript` 모듈 시스템은 **코드를 모듈화**하여 **재사용성**과 **유지보수성**을 높이는 방법입니다. 하지만 `JavaScript`가 실행되는 환경에 따라 각기 다른 모듈 시스템이 등장했으며, 각각의 시스템은 고유한 특성과 용도를 가지고 있습니다. 이번 글에서는 `CJS(CommonJS)`, `ESM(ECMAScript Modules)`, `UMD(Universal Module Definition)`를 상세하게 설명하고, 각각의 모듈 시스템이 어떻게 동작하는지 다양한 예시를 통해 알아보겠습니다.

---
### 1. CJS (CommonJS)

#### 정의 및 배경
`CommonJS(CJS)`는 `Node.js`에서 사용되는 대표적인 모듈 시스템입니다. `Node.js`는 `JavaScript`가 서버 측에서 사용되기 시작하면서 모듈화를 필요로 했고, 이를 해결하기 위해 `CJS`가 등장했습니다. `CJS`는 `JavaScript` 코드를 모듈화하여 서로 다른 파일 간에 `require()`로 불러올 수 있도록 만들어졌습니다.

#### 특징
- **동기적 모듈 로딩**: 모듈을 로드할 때, 코드가 차례대로 실행됩니다. 이는 서버 측에서 모듈을 로드하는 데 적합합니다.
- **단일 진입점**: `require()`를 통해 모듈을 가져오는 방식을 사용하며, `module.exports`를 통해 모듈을 외부로 내보냅니다.
- **Node.js 친화적**: 주로 `Node.js` 환경에서 사용되며, 파일 시스템 등 `Node.js`의 다른 기능과 함께 사용됩니다.

####  예시
**모듈 내보내기** (`moduleA.js`):
```js
// moduleA.js
const add = (a, b) => a + b;
const multiply = (a, b) => a * b;

module.exports = {
  add,
  multiply,
};
```

**모듈 가져오기** (`main.js`):
```js
// main.js
const math = require('./moduleA');

console.log(math.add(2, 3));        // 5
console.log(math.multiply(2, 3));   // 6
```

#### 장점
1. **간결한 구문**: `require()`와 `module.exports`를 사용하는 구문이 직관적입니다.
2. **Node.js에서 기본적으로 사용**: `Node.js`에서 기본적으로 지원되기 때문에 추가적인 설정이 필요 없습니다.
3. **광범위한 호환성**: 오래된 `JavaScript` 프로젝트나 패키지들은 여전히 `CJS`를 사용합니다.

#### 단점
1. **동기적 로딩 성능 문제**: `CJS`는 동기적으로 모듈을 로드하기 때문에, 큰 모듈을 로드할 경우 성능 문제가 발생할 수 있습니다.
2. **브라우저 호환성 문제**: `CJS`는 브라우저 환경에서는 기본적으로 지원되지 않아 **번들링 도구**(예: `Webpack`)가 필요합니다.

---

### 2. ESM (ECMAScript Modules)

#### 정의 및 배경
`ECMAScript Modules(ESM)`는 `JavaScript`의 **공식 모듈 시스템**으로, `ES6(ECMAScript 2015)`에서 도입되었습니다. `ESM`은 **브라우저와 Node.js 모두에서 사용**할 수 있으며, `JavaScript` 표준에 맞춘 모듈화 방식입니다.

#### 특징
- **비동기적 모듈 로딩**: 모듈이 비동기적으로 로드될 수 있어 성능 최적화에 유리합니다.
- **모듈 스코프**: `ESM`은 각 모듈이 고유한 스코프를 가지며, 전역 스코프와 충돌을 방지합니다.
- **트리 셰이킹 지원**: `ESM`을 사용하는 번들러는 사용하지 않는 코드를 자동으로 제거하는 트리 셰이킹 기능을 제공합니다.
- **엄격 모드**: `ESM` 모듈은 기본적으로 엄격 모드('strict mode')로 실행됩니다.

#### 예시
**모듈 내보내기** (`moduleB.js`):
```js
// moduleB.js
export const subtract = (a, b) => a - b;

export function divide(a, b) {
  return a / b;
}

const PI = 3.14;
export default PI;
```

**모듈 가져오기** (`main.js`):
```js
// main.js
import PI, { subtract, divide } from './moduleB.js';

console.log(subtract(5, 3));  // 2
console.log(divide(10, 2));   // 5
console.log(PI);              // 3.14
```

#### 장점
1. **모던 브라우저와 호환**: 최신 브라우저에서는 `ESM`을 기본적으로 지원하며, `<script type="module">` 태그로 사용 가능합니다.
2. **트리 셰이킹 지원**: 번들러를 사용할 경우 불필요한 코드를 자동으로 제거하여 최적화된 코드 크기를 제공합니다.
3. **유연한 모듈 내보내기**: ESM은 `export`와 `export default`를 통해 선택적으로 모듈을 내보낼 수 있어 유연성을 제공합니다.

#### 단점
1. **구형 브라우저와의 호환성 문제**: 구형 브라우저에서는 `ESM`을 지원하지 않기 때문에 **폴리필**이나 **번들링** 도구가 필요합니다.
2. **Node.js의 제한된 ESM 지원**: `Node.js`는 현재 `ESM`을 지원하지만, 여전히 일부 패키지는 `CJS`에 의존하고 있습니다. (Node.js 12 이상에서 ESM 사용 가능)

---

### 3. UMD (Universal Module Definition)

#### 정의 및 배경
`UMD(Universal Module Definition)`는 **브라우저와 Node.js 환경 모두에서 모듈을 사용**할 수 있도록 설계된 모듈 시스템입니다. `UMD`는 브라우저 환경에서 스크립트 태그로 직접 로드하거나, `CJS` 및 `AMD(Asynchronous Module Definition)`를 동시에 지원할 수 있습니다.

#### 특징
- **브라우저와 Node.js에서 모두 사용 가능**: `UMD`는 브라우저 및 `Node.js` 환경을 모두 지원하며, `AMD` 또는 `CJS` 방식으로도 사용할 수 있습니다.
- **범용성**: 어디서나 동일한 코드를 사용할 수 있기 때문에 라이브러리 개발에 적합합니다.
- **자동 환경 감지**: `UMD`는 실행 환경을 자동으로 감지하여, `CJS`, `AMD`, 또는 브라우저 글로벌 변수를 통해 모듈을 등록합니다.

#### 예시
**UMD 모듈 정의** (`moduleC.js`):
```js
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD
    define([], factory);
  } else if (typeof module === 'object' && module.exports) {
    // Node.js
    module.exports = factory();
  } else {
    // 브라우저 글로벌 변수
    root.myModule = factory();
  }
}(this, function () {
  return {
    myFunction: function () {
      console.log("Hello from UMD");
    }
  };
}));
