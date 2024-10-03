### Babel이란?

`Babel`은 `JavaScript` **컴파일러**입니다. **주로 최신 버전의 `JavaScript` 코드를 이전 버전의 `JavaScript`로 변환하는 데 사용**됩니다. 이를 통해 개발자들은 최신 문법과 기능을 사용하면서도 **오래된 브라우저나 환경에서도 코드가 동작할 수 있도록 보장**할 수 있습니다.

---
### 필요성

1. 브라우저 호환성

모든 브라우저가 최신 `JavaScript` 기능을 지원하는 것은 아닙니다. `Babel`을 사용하면 **최신 문법으로 작성된 코드를 대부분의 브라우저에서 실행 가능한 코드로 변환**할 수 있습니다.

2. 새로운 기능 사용

`ECMAScript`의 새로운 제안이나 실험적인 기능들을 미리 사용해볼 수 있습니다. `Babel` 플러그인을 통해 이러한 기능들을 현재의 프로젝트에 도입할 수 있습니다.

3. 일관된 코드베이스

팀 전체가 최신 문법을 사용하면서도 프로덕션 환경에서는 안정적인 코드를 배포할 수 있습니다.

---

### 주요 기능

1. 문법 변환

예를 들어, ES6+의 화살표 함수, 클래스, 템플릿 리터럴 등을 ES5 문법으로 변환합니다.

```javascript
// ES6+ 코드
const greet = (name) => `Hello, ${name}!`;

// Babel 변환 후 (ES5 호환)
var greet = function greet(name) {
  return "Hello, " + name + "!";
};
```

2. 폴리필 추가

새로운 전역 객체나 메소드(예: Promise, Array.from)를 지원하지 않는 환경을 위해 폴리필을 추가합니다.

3. 소스 코드 변환

`JSX`, `TypeScript` 등 다양한 형태의 소스 코드를 순수한 `JavaScrip`t로 변환할 수 있습니다.

---
### 설정하기

1. 설치

npm을 이용해 `Babel`을 설치할 수 있습니다:

```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

2. 설정 파일

프로젝트 루트에 `babel.config.json` 파일을 만들어 Babel 설정을 관리합니다:

```json
{
  "presets": ["@babel/preset-env"]
}
```

3. 스크립트 추가

`package.json`에 `Babel` 실행 스크립트를 추가합니다:

```json
{
  "scripts": {
    "build": "babel src -d lib"
  }
}
```

---
### 플러그인

각각의 문법 변환은 플러그인을 통해 이루어집니다. 예를 들어, `@babel/plugin-transform-arrow-functions`는 화살표 함수를 변환합니다.

---
### 프리셋

자주 사용되는 플러그인들의 집합입니다. `@babel/preset-env`는 가장 널리 사용되는 프리셋으로, 대상 환경에 맞춰 필요한 변환을 자동으로 결정합니다.

---

### Babel과 Webpack

많은 프로젝트에서 `Babel`은 `Webpack`과 함께 사용됩니다. `Webpack`의 로더 시스템을 통해 `Babel`을 적용할 수 있습니다:

```javascript
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /node_modules/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
```

---
### 한계와 주의사항

1. 성능 영향

`Babel`은 빌드 시간을 증가시킬 수 있습니다. 특히 큰 프로젝트에서는 이를 고려해야 합니다.

2. 폴리필 크기

필요 이상의 폴리필을 포함하면 번들 크기가 커질 수 있습니다. `@babel/preset-env`의 `useBuiltIns` 옵션을 활용해 이를 최적화할 수 있습니다.

---