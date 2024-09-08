---
tags:
  - react
  - nextjs
date: 2024-09-07
---
`Next.js`로 `SSR`을 사용시, 브라우저 전용 API를 사용하는 라이브러리로 인해 오류가 발생할 수 있습니다. 특히 `window is not defined` 오류는 SSR 환경에서 자주 발생하는 문제 중 하나입니다. 이것의 문제 원인과 해결 방법을 살펴보겠습니다.

### 1. 문제 상황: `window is not defined`

```js
import Calendar from 'library-name'

export default function Index() {
  return (
    <Calendar
     ...
    />
  )
}
```

이 코드를 SSR 환경에서 실행하면 다음과 같은 오류가 발생한다고 가정합니다.

```
Server Error
ReferenceError: window is not defined
```

이 오류는 서버에서 렌더링할 때 `window` 객체가 존재하지 않기 때문에 발생합니다. 해당 라이브러리는 브라우저 전용 라이브러리로, 서버 환경에서는 동작할 수 없습니다.

---
### 2. 문제 원인

브라우저 환경에서는 `window` 객체가 모든 페이지에 존재하지만, 서버 환경에서는 존재하지 않습니다. `SSR`에서는 모든 컴포넌트를 서버에서 미리 렌더링하기 때문에, `window`와 같은 브라우저 전용 API를 사용하는 라이브러리는 문제가 발생하게 됩니다.

---
### 3. 해결 방법: dynamic import로 SSR 비활성화

이 문제를 해결하려면 `Next.js`에서 제공하는 `dynamic import` 기능을 사용하여 해당 컴포넌트를 클라이언트 사이드에서만 렌더링하도록 설정할 수 있습니다. `dynamic import`를 사용하면 특정 컴포넌트를 서버가 아닌 클라이언트에서만 로드하도록 설정할 수 있습니다.

아래는 `dynamic import`로 문제를 해결한 코드입니다:

```js
import dynamic from 'next/dynamic'

// SSR을 비활성화한 동적 import
const Calendar = dynamic(() => import('library-name'), { ssr: false })

export default function Index() {
  return (
    <Calendar
    ...
    />
  )
}
```

이 코드를 통해 `<Calerndar />`는 클라이언트 사이드에서만 렌더링되며, 서버에서 발생하는 `window is not defined` 오류를 방지할 수 있습니다.

---
> SSR과 관련된 더 깊은 내용을 알고 싶다면 Next.js의 공식 문서를 참고하세요:
	[Next.js Dynamic Imports](https://nextjs.org/docs/advanced-features/dynamic-import)

