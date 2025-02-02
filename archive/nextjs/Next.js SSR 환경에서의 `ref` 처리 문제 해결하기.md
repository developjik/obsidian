`Next.js`의 `SSR` 환경에서 `react`의 `ref`는 예상과 다르게 동작할 수 있습니다. 특히 `SSR`에서는 `useRef`와 `useEffect`의 실행 시점 차이로 인해 예상하지 못한 문제가 발생할 수 있습니다.

### 1. 문제 상황: `ref`가 정상적으로 동작하지 않음

다음은 `useRef`와 `useEffect`를 사용한 코드입니다:

```js
export default function Index() {
  const cal = useRef()

  useEffect(() => {
    console.log(cal.current)
  }, [cal])

  return <Calendar ref={cal} />
}
```

이 코드에서는 `useEffect`가 실행되면 `cal.current`가 `Calendar` 컴포넌트 인스턴스로 출력되지 않는 문제가 발생합니다. 이는 `SSR` 환경에서 발생하는 문제로, `ref`가 `SSR`에서 제대로 동작하지 않기 때문에 발생합니다.

### 2. 문제 원인

`useRef`는 리액트가 렌더링하는 동안 변경되지 않는 참조 객체를 반환하지만, SSR에서는 렌더링과 `useEffect`가 실행되는 시점이 다르기 때문에 서버에서 `ref`가 정상적으로 동작하지 않습니다. `ref.current`에는 내부적으로 설정된 값만 보이게 되는데, 이는 `Next.js`가 서버에서 `SSR`을 위해 사용하는 로드 가능한 컴포넌트와 관련이 있습니다.

#### 3. 해결 방법: `forwardRef`와 `useImperativeHandle` 사용하기

이 문제를 해결하려면 `forwardRef`와 `useImperativeHandle`을 사용하여 부모 컴포넌트에 전달되는 `ref`를 사용자 정의할 수 있습니다. 먼저, `라이브러리`를 감싸는 컴포넌트를 생성하고, `forwardRef`를 사용하여 `ref`를 전달합니다.

##### 1) 하위 컴포넌트에 `ref` 전달

```js
import React from 'react'
import Calendar from '라이브러리 이름'

export default (props) => (
  <Calendar {...props} ref={props.forwardedRef} />
)
```

##### 2) `forwardRef`로 상위 컴포넌트에서 `ref` 전달

```js
import React, { useRef } from 'react'
import dynamic from 'next/dynamic'

const Calendar = dynamic(() => import('wrap 한 컴포넌트 이름'), { ssr: false })

const CalendarWithForwardedRef = React.forwardRef((props, ref) => (
  <Calendar {...props} forwardedRef={ref} />
))

export default function Index() {
  const ref = useRef()

  return <CalendarWithForwardedRef ref={ref} />
}
```

이 코드에서는 `forwardRef`를 사용하여 부모 컴포넌트에서 자식 컴포넌트로 `ref`를 전달하고, 이제 `ref.current`는 올바르게 `Calendar` 인스턴스를 가리키게 됩니다.

> SSR 환경에서의 `ref` 처리에 대한 더 자세한 내용은 리액트 공식 문서를 참고하세요
> [React forwardRef](https://reactjs.org/docs/forwarding-refs.html)

