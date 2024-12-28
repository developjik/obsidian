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