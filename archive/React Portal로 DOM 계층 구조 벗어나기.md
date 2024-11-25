`React` `Portal`은 **컴포넌트를 DOM 계층 구조 밖의 다른 DOM 노드로 렌더링할 수 있게 해주는 강력한 기능**입니다. **모달**, **툴팁**, **팝오버**와 같은 오버레이 UI를 구현할 때 특히 유용합니다.

## 필요한 이유

**일반적인 `React` 렌더링에서는 부모 컴포넌트의 DOM 트리 안에 자식 컴포넌트가 렌더링됩니다.** 하지만 다음과 같은 상황에서는 이러한 제약이 문제가 될 수 있습니다:

1. CSS `overflow: hidden`이나 `z-index` 스타일링 이슈
2. 모달이 부모 컴포넌트의 스타일에 영향을 받는 경우
3. 전역적으로 표시되어야 하는 알림이나 툴팁

이럴 때 `Portal`을 사용하면 **컴포넌트를 원하는 DOM 노드에 렌더링**할 수 있습니다.

---
## 사용법

```jsx
import { createPortal } from 'react-dom';

function Modal({ children, isOpen }) {
  if (!isOpen) return null;
  
  return createPortal(
    <div className="modal">
      {children}
    </div>,
    document.getElementById('modal-root')
  );
}
```

`Portal`을 사용하기 위해서는:

1. `HTML`에 포털 **타겟 엘리먼트** 추가:
```html
<div id="modal-root"></div>
```

2. `createPortal` 함수 호출:
```jsx
createPortal(렌더링할_컨텐츠, DOM_타겟_엘리먼트)
```

---
## 특징

1. **이벤트 버블링**: `Portal`로 렌더링된 컴포넌트도 `React` 트리의 일부이므로, **이벤트 버블링이 정상적으로 작동**합니다.

```jsx
function Parent() {
  return (
    <div onClick={() => console.log('부모 클릭')}>
      <Modal>
        <button onClick={() => console.log('자식 클릭')}/>
      </Modal>
    </div>
  );
}
```

2. **컨텍스트**: `Portal` 컴포넌트는 `React` 트리의 일부이므로 `Context API`를 통한 상태 공유가 가능합니다.

---
## 예제

아래는 모달 구현 예제입니다:

```jsx
function App() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(true)}>
        모달 열기
      </button>

      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        <h2>포털을 통해 렌더링된 모달</h2>
        <p>이 모달은 DOM 트리의 다른 위치에 렌더링됩니다.</p>
        <button onClick={() => setIsOpen(false)}>닫기</button>
      </Modal>
    </div>
  );
}
```

---
## 주의사항

1. `Portal` 타겟 엘리먼트가 `DOM`에 존재하는지 확인하세요.
2. `SSR(Server-Side Rendering)` 환경에서는 `document` 객체에 접근할 수 없으므로 추가 설정이 필요합니다.
3. `Portal`을 남용하면 앱의 구조를 이해하기 어려워질 수 있으니 필요한 경우에만 사용하세요.