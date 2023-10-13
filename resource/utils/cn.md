---
sticker: lucide//settings
---

## cn?

`tailwind`를 `merge`할 때 발생할 수 있는 클래스 충돌 문제를 해결하는 함수

```typescript

import { type ClassValue, clsx } from "clsx"  
import { twMerge } from "tailwind-merge"  

 export function cn(...inputs: ClassValue[]) {  
  return twMerge(clsx(inputs))  
}

```

> [[clsx]]   
> [[tailwind-merge]]