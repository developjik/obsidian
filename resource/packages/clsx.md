---
tags: package
sticker: emoji//1f4e6
---
## clsx? 
A tiny (234B) utility for constructing `className` strings conditionally.  
Also serves as a [faster](https://github.com/lukeed/clsx/blob/HEAD/bench) & smaller drop-in replacement for the `classnames` module.

## clsx install
```shell
npm i --save clsx
```

```shell
yarn add --dev clsx
```

```shell
pnpm i -D clsx
```

## clsx example
```typescript
import clsx from 'clsx';
// or
import { clsx } from 'clsx';

// Strings (variadic)
clsx('foo', true && 'bar', 'baz');
//=> 'foo bar baz'

// Objects
clsx({ foo:true, bar:false, baz:isTrue() });
//=> 'foo baz'

// Objects (variadic)
clsx({ foo:true }, { bar:false }, null, { '--foobar':'hello' });
//=> 'foo --foobar'

// Arrays
clsx(['foo', 0, false, 'bar']);
//=> 'foo bar'

// Arrays (variadic)
clsx(['foo'], ['', 0, false, 'bar'], [['baz', [['hello'], 'there']]]);
//=> 'foo bar baz hello there'

// Kitchen sink (with nesting)
clsx('foo', [1 && 'bar', { baz:false, bat:null }, ['hello', ['world']]], 'cya');
//=> 'foo bar hello world cya'
```

## clsx feature
- 조건부 렌더를 작성하기 쉬워진다.

## clsx typescript

```typescript
import { ClassValue, clsx } from 'clsx';
```

이 `ClassValue`에 대한 정의는 다음과 같습니다.

```typescript
export type ClassValue = ClassArray | ClassDictionary | string | number | null | boolean | undefined;
export type ClassDictionary = Record<string, any>;
export type ClassArray = ClassValue[];

export declare function clsx(...inputs: ClassValue[]): string;
export default clsx;
```

이를 활용한 `tailwind util` 함수는 다음과 같습니다.
```typescript

import { type ClassValue, clsx } from "clsx"  
import { twMerge } from "tailwind-merge"  

 export function cn(...inputs: ClassValue[]) {  
  return twMerge(clsx(inputs))  
}

```

>[clsx npm](https://www.npmjs.com/package/clsx)   
>[clsx git](https://github.com/lukeed/clsx)

> [clsx와 유사한 classnames npm](https://www.npmjs.com/package/classnames)
> [clsx와 유사한 classnames git](https://github.com/JedWatson/classnames)