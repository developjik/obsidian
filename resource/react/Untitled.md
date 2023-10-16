---
sticker: emoji//1f640
tags:
  - react
---
## displayName example
```typescript

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(  
  ({ className, variant, size, asChild = false, ...props }, ref) => {  
    const Comp = asChild ? Slot : "button"  
    return (  
      <Comp  
        className={cn(buttonVariants({ variant, size, className }))}  
        ref={ref}  
        {...props}  
      />    )  
  }  
)

Button.displayName = "Button"

```

## `displayName`?

문자열 `displayName`은 디버깅 메시지에 사용됩니다. 

일반적으로 구성 요소를 정의하는 함수나 클래스의 이름에서 유추하므로 명시적으로 설정할 필요가 없습니다. 

디버깅 목적으로 다른 이름을 표시하거나 상위 구성 요소를 생성할 때 이를 명시적으로 설정할 수 있습니다. 

자세한 내용은 [링크](https://legacy.reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)를 참조하세요.