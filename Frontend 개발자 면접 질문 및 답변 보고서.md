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

### Q5: 브라우저 렌더링 과정을 설명해주세요.

**답변:** 브라우저 렌더링은 `HTML`, `CSS`, `JavaScript`를 화면에 표시하기 위한 일련의 과정입니다.

**해설:** 

렌더링 과정:

1. **DOM 트리 생성 (HTML 파싱)**
2. **CSSOM 트리 생성 (CSS 파싱)**
3. **렌더 트리 생성 (DOM + CSSOM)**
4. **레이아웃 (요소들의 위치와 크기 계산)**
5. **페인팅 (실제 픽셀로 그리기)**
6. **컴포지팅 (레이어 합성)**

### Q6: Critical Rendering Path를 최적화하는 방법은?

**답변:** `Critical Rendering Path` 최적화는 초기 렌더링 성능을 향상시키기 위한 기법들입니다.

**해설:** 최적화 방법:

1. `CSS`는 head에, `JavaScript`는 body 끝에 배치
2. 중요하지 않은 `JavaScript`에 `async/defer` 사용
3. 중요 `CSS` 인라인화
4. 미디어 쿼리 활용
5. `JavaScript` 번들 분할

### Q7: localStorage와 sessionStorage의 차이점은?

**답변:** 두 저장소는 웹 스토리지 API의 일부로, 데이터 지속성과 범위에서 차이가 있습니다.

**해설:**
```js
// localStorage 예시
localStorage.setItem('user', JSON.stringify({id: 1, name: 'John'}));
// 브라우저를 닫았다 열어도 데이터 유지

// sessionStorage 예시
sessionStorage.setItem('token', 'abc123');
// 탭을 닫으면 데이터 삭제
```

주요 차이점:

- 데이터 지속성
- 저장 범위
- 용량 제한
- 도메인 범위

### Q8: 쿠키와 웹 스토리지의 차이점은?

**답변:** 쿠키와 웹 스토리지는 다른 목적과 특성을 가진 데이터 저장 메커니즘입니다.

**해설:** 차이점:

1. 용량 제한 (쿠키 4KB, 웹 스토리지 5MB~10MB)
2. 서버 전송 (쿠키는 자동 전송, 웹 스토리지는 클라이언트만)
3. 만료 시간 설정 가능 여부
4. 접근성과 보안 특성

### Q9: ARIA 역할과 속성에 대해 설명해주세요.

**답변:** `ARIA`는 접근성 있는 리치 인터넷 애플리케이션을 위한 규격으로, `HTML` 요소에 추가적인 의미를 부여합니다.

**해설:**

```jsx
<!-- ARIA 사용 예시 -->
<button 
  role="tab"
  aria-selected="true"
  aria-controls="panel-1"
>
  탭 1
</button>
<div
  role="tabpanel"
  id="panel-1"
  aria-labelledby="tab-1"
>
  내용 1
</div>
```

### Q10: 키보드 접근성을 보장하는 방법은?

**답변:** 키보드 접근성은 마우스를 사용할 수 없는 사용자를 위해 필수적입니다.

**해설:**

```js
// 키보드 이벤트 처리 예시
const HandleKeyPress = (event) => {
  if (event.key === 'Enter' || event.key === ' ') {
    // 실행 로직
  }
};

// 포커스 관리
const handleModalOpen = () => {
  modalRef.current.focus();
};
```


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

## TypeScript

### Q1: any와 unknown 타입의 차이점은 무엇인가요?

**답변:** `any`와 `unknown`은 **모두 모든 타입의 값을 할당할 수 있지만, `unknown`은 타입 안정성을 제공하는 반면 `any`는 타입 검사를 완전히 무시**합니다.

**해설:**

```tsx
let anyVar: any = 10;
let unknownVar: unknown = 10;

// any는 모든 연산 허용
anyVar.foo.bar; // 오류 없음
anyVar(); // 오류 없음

// unknown은 타입 체크 필요
unknownVar.foo.bar; // 오류
unknownVar(); // 오류

// unknown은 타입 검사 후 사용 가능
if (typeof unknownVar === 'function') {
  unknownVar(); // 정상 동작
}
```

### Q2: 제네릭(Generics)을 사용하는 이유와 장점은 무엇인가요?

**답변:** `제네릭`은 **타입을 매개변수화하여 재사용 가능한 컴포넌트**를 만들 수 있게 해주는 기능입니다.

**해설:**

```tsx
// 제네릭 사용 예시
interface Box<T> {
  value: T;
}

// 함수에서의 제네릭 사용
function identity<T>(arg: T): T {
  return arg;
}

// 제네릭 제약조건
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

장점:

- 타입 안정성 보장
- 코드 재사용성 향상
- 컴파일 시점에서 타입 체크
- 더 명확한 API 설계 가능

---

## 웹 보안

### Q1: XSS와 CSRF 공격에 대해 설명하고 방어 방법을 말씀해주세요.

**답변:** `XSS(Cross-Site Scripting)`는 **악성 스크립트를 웹 페이지에 주입하는 공격**이며, `CSRF(Cross-Site Request Forgery)`는 **사용자의 권한을 이용해 악의적인 요청을 보내는 공격**입니다.

**해설:** 

XSS 방어 방법:

```jsx
// 잘못된 예시
element.innerHTML = userInput;

// 올바른 방법
element.textContent = userInput;

// React의 경우
const dangerousHTML = {
  __html: sanitizeHTML(userInput) // 적절한 sanitize 라이브러리 사용
};
<div dangerouslySetInnerHTML={dangerousHTML} />;
```

CSRF 방어 방법:

- CSRF 토큰 사용
- Same-Site 쿠키 설정
- Origin 검증

### Q2: Content Security Policy(CSP)에 대해 설명해주세요.

**답변:** `CSP`는 `XSS` 같은 **특정 유형의 공격을 감지하고 완화하기 위한 추가적인 보안 계층**입니다.

**해설:**

```html
<!-- CSP 헤더 예시 -->
Content-Security-Policy: default-src 'self';
                        script-src 'self' 'unsafe-inline' 'unsafe-eval';
                        style-src 'self' 'unsafe-inline';
                        img-src 'self' data: https:;
```

주요 기능:

- 신뢰할 수 있는 콘텐츠 소스 지정
- 인라인 스크립트 실행 제어
- 리소스 로딩 제어
- 리포팅 기능

---
## 테스팅 전략

### Q1: 단위 테스트, 통합 테스트, E2E 테스트의 차이점과 각각의 사용 사례는?

**답변:** 각 테스트 유형은 다른 범위와 목적을 가지고 있으며, 효과적인 테스트 전략을 위해서는 이들을 적절히 조합해야 합니다.

**해설:**

```js
// 단위 테스트 예시 (Jest)
describe('calculateTotal', () => {
  test('correctly calculates total with tax', () => {
    expect(calculateTotal(100, 0.1)).toBe(110);
  });
});

// 통합 테스트 예시 (React Testing Library)
test('form submission works correctly', async () => {
  render(<UserForm />);
  fireEvent.change(screen.getByLabelText(/username/i), {
    target: { value: 'testuser' },
  });
  fireEvent.click(screen.getByText(/submit/i));
  await waitFor(() => {
    expect(screen.getByText(/success/i)).toBeInTheDocument();
  });
});

// E2E 테스트 예시 (Cypress)
describe('Login Flow', () => {
  it('successfully logs in', () => {
    cy.visit('/login');
    cy.get('[data-testid="username"]').type('user');
    cy.get('[data-testid="password"]').type('pass');
    cy.get('button').click();
    cy.url().should('include', '/dashboard');
  });
});
```

### Q2: React 컴포넌트 테스트 전략에 대해 설명해주세요.

**답변:** `React` 컴포넌트 테스트는 **사용자 관점에서의 동작을 검증하는 것이 중요**하며, React Testing Library의 철학을 따르는 것이 좋습니다.

**해설:**

```js
// 잘못된 테스트 방식 (구현 세부사항에 의존)
test('state updates correctly', () => {
  const wrapper = shallow(<Counter />);
  expect(wrapper.state('count')).toBe(0);
});

// 올바른 테스트 방식 (사용자 관점)
test('counter increments when button is clicked', () => {
  render(<Counter />);
  const button = screen.getByText(/increment/i);
  fireEvent.click(button);
  expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
});

```

테스트 우선순위:

1. 핵심 비즈니스 로직
2. 사용자 상호작용이 많은 컴포넌트
3. 재사용성이 높은 공통 컴포넌트
4. 에러 경계(Error Boundaries)

좋은 테스트의 특징:

- 구현이 아닌 동작을 테스트
- 실제 사용자 시나리오 반영
- 유지보수가 용이
- 신뢰할 수 있는 결과 제공


## 모던 JavaScript

### Q23: Promise와 async/await의 차이점과 사용 케이스는?

**답변:** Promise와 async/await는 비동기 처리를 위한 패턴으로, 각각의 장단점이 있습니다.

**해설:**

javascript

Copy

`// Promise 체이닝 fetchUser()   .then(user => fetchUserPosts(user.id))  .then(posts => {    console.log(posts);  })  .catch(error => {    console.error(error);  }); // async/await async function getUserPosts() {   try {    const user = await fetchUser();    const posts = await fetchUserPosts(user.id);    console.log(posts);  } catch (error) {    console.error(error);  } }`

### Q24: 이벤트 루프와 태스크 큐에 대해 설명해주세요.

**답변:** 이벤트 루프는 JavaScript의 동시성 모델을 구현하는 메커니즘입니다.

**해설:**

javascript

Copy

`console.log('시작'); setTimeout(() => {   console.log('타이머'); }, 0); Promise.resolve()   .then(() => console.log('프로미스')); console.log('끝'); // 출력 순서: // 시작 // 끝 // 프로미스 // 타이머`

## React 심화

### Q25: React.memo(), useMemo(), useCallback()의 차이점과 사용 시기는?

**답변:** 세 가지 모두 성능 최적화를 위한 도구이지만, 각각 다른 용도로 사용됩니다.

**해설:**

javascript

Copy

`// React.memo() - 컴포넌트 메모이제이션 const MemoizedComponent = React.memo(({data}) => {   return <div>{data}</div>; }); // useMemo() - 값 메모이제이션 const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]); // useCallback() - 함수 메모이제이션 const memoizedCallback = useCallback(   () => {    doSomething(a, b);  },  [a, b], );`

### Q26: React의 에러 경계(Error Boundaries)에 대해 설명해주세요.

**답변:** 에러 경계는 하위 컴포넌트 트리의 JavaScript 에러를 포착하고 처리하는 React 컴포넌트입니다.

**해설:**

javascript

Copy

`class ErrorBoundary extends React.Component {   constructor(props) {    super(props);    this.state = { hasError: false };  }   static getDerivedStateFromError(error) {    return { hasError: true };  }   componentDidCatch(error, errorInfo) {    logErrorToService(error, errorInfo);  }   render() {    if (this.state.hasError) {      return <h1>Something went wrong.</h1>;    }    return this.props.children;  } }`


## CSS 심화

### Q29: CSS 애니메이션 성능 최적화 방법은?

**답변:** CSS 애니메이션 성능은 브라우저의 렌더링 파이프라인에 큰 영향을 미칩니다.

**해설:**

css

Copy

`/* 나쁜 예시 */ .animation-bad {   animation: move 1s; } @keyframes move {   from { left: 0; }  to { left: 100px; } } /* 좋은 예시 */ .animation-good {   animation: moveTransform 1s; } @keyframes moveTransform {   from { transform: translateX(0); }  to { transform: translateX(100px); } }`