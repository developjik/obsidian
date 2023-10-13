---
sticker: emoji//1f4e6
---

## tailwind-merge?
Utility function to efficiently merge [Tailwind CSS](https://tailwindcss.com/) classes in JS without style conflicts.

## tailwind-merge install
```shell
npm i tailwind-merge
```

```shell
yarn add tailwind-merge
```

```shell
pnpm i tailwind-merge
```

## tailwind-merge example 
```typescript
import { twMerge } from 'tailwind-merge'

twMerge('px-2 py-1 bg-red hover:bg-dark-red', 'p-3 bg-[#B91C1C]')
// → 'hover:bg-dark-red p-3 bg-[#B91C1C]'
```

## tailwind-merge feature
-  Last Conflicting Class Wins: 맨 마지막에 적용시킨 클래스가 적용됩니다.
-  기본적으로 결과값은 `캐싱`되기 때문에 불필요한 `리렌더링`을 걱정하지 않아도 됩니다.
-  라이브러리는 내부적으로 `LRU cache` 알고리즘을 사용하고 있고
-  캐시 사이즈를 만약 수정하고 싶다면 `extendTailwindMerge`를 사용하면 됩니다.
- 캐시힛 없이 twMerge 호출이 빠르게 유지될 수 있도록 비싼 계산은 upfront로 수행한다.

>  tailwind-merge로 충분한 클래스에 **@apply 지시문을 사용**하는 것이 추천되지 않습니다.

> 충돌을 해결하지 않으면서 조건부로 join을 하는 것을 도와주는 `twJoin`도 있습니다.
```typescript
twJoin(
    'border border-red-500',
    hasBackground && 'bg-red-100',
    hasLargeText && 'text-lg',
    hasLargeSpacing && ['p-2', hasLargeText ? 'leading-8' : 'leading-7'],
)
```

   
>[tailwind-merge npm](https://www.npmjs.com/package/tailwind-merge)   
>[tailwind-merge git](https://github.com/dcastil/tailwind-merge)