---
sticker: emoji//1f623
---
## HTML/웹 표준
### Q1: `DOCTYPE`이란 무엇이며 왜 필요한가요?

**답변:** `DOCTYPE`은 문서 형식 선언(Document Type Declaration)으로, **HTML 문서의 최상단에 위치하며 해당 문서의 HTML 버전을 브라우저에게 알려주는 역할**을 합니다.

**해설:**
- `DOCTYPE` **선언이 없으면 브라우저는 쿼크 모드(Quirks mode)로 작동**하여 각 브라우저마다 다른 방식으로 페이지를 렌더링할 수 있습니다.
- `HTML5`의 경우 `<!DOCTYPE html>`로 간단하게 선언합니다.
- 이를 통해 브라우저는 `표준 모드(Standards mode)`로 작동하여 **일관된 렌더링을 보장**합니다.

### Q2: 시맨틱 마크업이란 무엇이며 왜 중요한가요?

**답변:** `시맨틱 마크업`은 `HTML` 요소를 의미에 맞게 사용하는 것을 말합니다. 예를 들어 제목은 `<h1>~<h6>`, 내비게이션은 `<nav>`, 본문은 `<article>` 등을 사용하는 것입니다.

**해설:**
- 검색엔진 최적화(SEO) 향상
- 스크린 리더 등 보조기술 사용자의 웹 접근성 개선
- 코드의 가독성과 유지보수성 향상
- 개발자 간의 명확한 의사소통 가능

### Q3: 웹 성능 최적화 방법에 대해 설명해주세요.

**답변:** 웹 성능 최적화는 페이지 로딩 시간을 줄이고 사용자 경험을 개선하기 위한 다양한 기법들을 포함합니다.

**해설:**

1. 리소스 최적화
    - 이미지 최적화 (WebP 사용, 적절한 크기로 리사이징)
    - JavaScript/CSS 파일 번들링 및 압축
    - 코드 스플리팅
2. 로딩 최적화
    - 레이지 로딩 구현
    - 프리로딩 적용
    - 캐싱 전략 수립
3. 렌더링 최적화
    - CSS 애니메이션은 transform 사용
    - 불필요한 리렌더링 방지
    - 레이아웃 쓰로틀링 최소화
4. React 관련 최적화
    - React.memo 사용
    - useMemo와 useCallback 적절히 활용
    - 상태 관리 최적화

### Q4: CSR과 SSR의 차이점과 각각의 장단점을 설명해주세요.

**답변:** `CSR(Client Side Rendering)`은 **클라이언트에서 JavaScript를 통해 렌더링하는 방식**이고, `SSR(Server Side Rendering)`은 **서버에서 HTML을 생성하여 클라이언트로 전송하는 방식**입니다.

**해설:** 

CSR의 특징:

- 장점:
    - 초기 로딩 후 빠른 페이지 전환
    - 서버 부하 감소
    - 풍부한 인터랙션 구현 용이
- 단점:
    - 초기 로딩 시간이 김
    - SEO가 어려움
    - 자바스크립트 필수

SSR의 특징:

- 장점:
    - 빠른 초기 로딩
    - 좋은 SEO
    - 자바스크립트 비활성화 환경 대응
- 단점:
    - 서버 부하 증가
    - 페이지 전환시 새로고침 필요
    - 개발 복잡도 증가


---
## CSS

### Q1: Box Model에 대해 설명해주세요.

**답변:** `Box Model`은 `HTML` **요소를 박스로 표현하는 방법**으로, `Content`, `Padding`, `Border`, `Margin`의 4가지 속성으로 구성됩니다.

**해설:**

- `Content`: 실제 내용이 들어가는 영역
- `Padding`: Content와 Border 사이의 여백
- `Border`: 박스의 테두리
- `Margin`: 다른 요소와의 간격
- `box-sizing: border-box`를 사용하면 padding과 border를 width/height에 포함시킬 수 있음

### Q2: Flexbox와 Grid의 차이점은 무엇인가요?

**답변:** `Flexbox`는 **1차원 레이아웃 시스템으로 행이나 열 중 하나의 축을 기준으로 요소를 배치**하며, `Grid`는 **2차원 레이아웃 시스템으로 행과 열 모두를 고려하여 요소**를 배치합니다.

**해설:**

- `Flexbox` 사용 케이스:
    - 네비게이션 메뉴
    - 카드 리스트
    - 간단한 정렬이 필요한 경우
- `Grid` 사용 케이스:
    - 전체 페이지 레이아웃
    - 갤러리/대시보드
    - 복잡한 2차원 레이아웃이 필요한 경우

---

## JavaScript

### Q1: 호이스팅(Hoisting)에 대해 설명해주세요.

**답변:** `호이스팅`은 `JavaScript`에서 **변수와 함수 선언이 코드의 최상단으로 끌어올려지는 것처럼 동작하는 특성**입니다.

**해설:**

```javascript
console.log(a); // undefined
var a = 1;

// 위 코드는 실제로 다음과 같이 동작합니다
var a;
console.log(a);
a = 1;
```

- `var`로 선언된 변수는 **선언부만 호이스팅**되고 **할당은 원래 위치**에서 이루어짐
- `let`과 `const`는 `TDZ(Temporal Dead Zone)`로 인해 선언 전 접근 시 에러 발생
- `함수 선언문`은 **전체가 호이스팅**되어 **어디서든 호출 가능**

### Q2: 이벤트 버블링과 캡처링에 대해 설명해주세요.

**답변:** `이벤트 버블링`은 특**정 요소에서 이벤트가 발생했을 때 그 이벤트가 상위 요소로 전파되는 현상**이고, `캡처링`은 반대로 **최상위 요소에서 시작하여 이벤트가 발생한 요소까지 내려가는 현상**입니다.

**해설:**

```js
// 이벤트 버블링 예시
element.addEventListener('click', function(e) {
    console.log('버블링 단계');
});

// 캡처링 단계에서 이벤트 감지
element.addEventListener('click', function(e) {
    console.log('캡처링 단계');
}, true);  // true를 전달하면 캡처링 단계에서 이벤트 감지

// 이벤트 전파 중단
element.addEventListener('click', function(e) {
    e.stopPropagation();
});
```

- 기본적으로 `버블링`이 더 자주 사용됨
- `event.stopPropagation()`으로 **전파를 중단**할 수 있음
- `이벤트 위임(Event Delegation) 패턴` 구현에 활용

---
## React

### Q1: React의 생명주기(Lifecycle)에 대해 설명해주세요.

**답변:** `React` 컴포넌트의 생명주기는 `마운팅`, `업데이트`, `언마운팅`의 세 단계로 구성되며, 각 단계에서 특정 메서드들이 호출됩니다.

**해설:**

- `마운팅` 단계:
    - constructor
    - getDerivedStateFromProps
    - render
    - componentDidMount
- `업데이트` 단계:
    - getDerivedStateFromProps
    - shouldComponentUpdate
    - render
    - getSnapshotBeforeUpdate
    - componentDidUpdate
- `언마운팅` 단계:
    - componentWillUnmount

`Hooks`를 사용하는 경우:

```jsx
// 클래스 컴포넌트의 생명주기를 훅으로 대체
useEffect(() => {
  // componentDidMount
  return () => {
    // componentWillUnmount
  };
}, []); // 의존성 배열

```

### Q2: Virtual DOM이란 무엇이며 어떤 이점이 있나요?

**답변:** `Virtual DOM`은 **실제 DOM의 가벼운 복사본**으로, **`React`가 상태 변화를 감지했을 때 먼저 `Virtual DOM`에서 변경사항을 적용하고 실제 DOM과 비교하여 필요한 부분만 업데이트하는 방식**입니다.

**해설:**

- 장점:
    - 불필요한 DOM 조작 최소화로 성능 향상
    - 브라우저의 리플로우/리페인트 최소화
    - 크로스 플랫폼 지원 용이
- 동작 방식:
    1. 상태 변화 발생
    2. 새로운 Virtual DOM 트리 생성
    3. 이전 Virtual DOM과 비교(Diffing)
    4. 실제 변경이 필요한 부분만 실제 DOM에 적용

### Q3: Redux의 핵심 원칙과 동작 방식에 대해 설명해주세요.

**답변:** `Redux`는 단방향 데이터 흐름을 가진 상태 관리 라이브러리로, `Store`, `Action`, `Reducer`라는 세 가지 핵심 개념을 가지고 있습니다.

**해설:**

- 세 가지 원칙:
    1. **Single Source of Truth** (단일 진실 공급원)
    2. **State is Read-Only** (상태는 읽기 전용)
    3. Changes are made with **Pure Functions** (순수 함수로 변경)

```js
// Action 정의
const addTodo = (text) => ({
  type: 'ADD_TODO',
  payload: text
});

// Reducer 구현
const todoReducer = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, { text: action.payload, completed: false }];
    default:
      return state;
  }
};

// Store 사용
const store = createStore(todoReducer);
store.dispatch(addTodo('리덕스 공부하기'));

```
### Q4: 다른 상태 관리 도구들(MobX, Recoil)과 비교했을 때 Redux의 장단점은?

**답변:** 각 상태 관리 도구는 서로 다른 철학과 특징을 가지고 있어 프로젝트의 요구사항에 따라 적절한 선택이 필요합니다.

**해설:** 

Redux:

- 장점:
    - 예측 가능한 상태 관리
    - 강력한 개발자 도구
    - 큰 커뮤니티와 생태계
- 단점:
    - 보일러플레이트 코드가 많음
    - 학습 곡선이 가파름

MobX:

- 장점:
    - 적은 보일러플레이트
    - 유연한 상태 관리
    - 객체지향적 접근
- 단점:
    - 자유도가 높아 일관성 유지가 어려울 수 있음
    - 디버깅이 상대적으로 어려움

Recoil:

- 장점:
    - React에 최적화된 설계
    - 비동기 처리가 용이
    - 작은 단위의 상태 관리 가능
- 단점:
    - 비교적 새로운 기술로 생태계가 작음
    - Facebook 의존성

---