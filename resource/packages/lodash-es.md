---
sticker: emoji//1f4e6
tags:
  - package
---
## lodash-es install
```shell
npm i lodash-es
```

```shell
yarn add lodash-es
```

```shell
pnpm i lodash-es
```

## lodash-es 란?

`lodash-es`는 JavaScript 언어를 위한 유틸리티 라이브러리인 Lodash의 ECMAScript 모듈 버전입니다. Lodash는 JavaScript에서 많은 유용한 기능과 함수를 제공하는 라이브러리로, 배열 조작, 객체 조작, 함수형 프로그래밍 지원, 문자열 조작 등 다양한 기능을 포함하고 있습니다.

`lodash-es`는 Lodash의 ECMAScript 모듈 시스템을 지원하는 버전으로, 일반적으로 모듈 번들러(예: Webpack, Rollup)를 통해 사용됩니다. ECMAScript 모듈은 JavaScript의 모듈 시스템을 정의하는 표준 스펙이며, `lodash-es`는 이를 따라 Lodash의 모듈화를 가능케 합니다.

`lodash-es`를 사용하면 필요한 기능만 선택적으로 가져와서 사용할 수 있으며, 번들 크기를 최적화할 수 있습니다(Tree shaking). 이는 특히 대규모 프로젝트에서 유용하며, 필요한 기능만 포함하여 불필요한 부분을 제거함으로써 애플리케이션의 성능을 향상시킬 수 있습니다.

> Tree shaking은 JavaScript 환경에서 사용되는 최적화 기술 중 하나로, 불필요한 코드를 제거하여 번들 크기를 최소화하는 목적을 가지고 있습니다. 주로 모듈 번들러(예: Webpack)와 함께 사용되며, 특히 프론트엔드 개발에서 성능 최적화를 위해 중요한 역할을 합니다.
> 
> Tree shaking의 주요 아이디어는 애플리케이션에서 사용되지 않는 코드를 감지하고 제거하여 번들 크기를 줄이는 것입니다. 예를 들어, 모듈 시스템을 사용하는 경우, 애플리케이션에서 실제로 사용되지 않는 함수나 변수가 다수 포함된 모듈이 있을 수 있습니다. Tree shaking은 이러한 불필요한 부분을 찾아내고 제거함으로써 최종 번들의 크기를 최소화합니다.
> 
> 이러한 최적화는 특히 대규모 프로젝트에서 중요하며, 작은 번들 크기는 웹 페이지의 로딩 속도를 향상시키고 사용자 경험을 향상시킬 수 있습니다. Tree shaking을 사용하려면 코드를 모듈로 구성하고, 모듈 시스템이 지원되는 환경에서 모듈을 잘 정의해야 합니다. 그런 다음, 모듈 번들러에서 Tree shaking을 활성화하여 사용하지 않는 코드를 제거할 수 있습니다.

## lodash-es vs lodash

`lodash`와 `lodash-es`의 주요 차이점은 모듈 시스템의 지원과 번들 크기 최적화에 있습니다.

1. **모듈 시스템 지원:**
    
    - **Lodash:** 원래의 Lodash는 전역 객체에 함수들이 정의된 형태로 제공됩니다. 이는 전역 네임스페이스의 오염이나 충돌의 위험성이 있습니다.
        
    - **Lodash-es:** Lodash의 ECMAScript 모듈 버전으로, ECMAScript 모듈 시스템을 사용합니다. 이는 모듈 단위로 필요한 함수만 가져와 사용할 수 있으며, 다른 모듈과의 충돌을 방지합니다.
        
2. **번들 크기 최적화:**
    
    - **Lodash:** 전체 Lodash를 사용할 경우, 모든 기능이 포함된 큰 크기의 라이브러리를 번들에 포함해야 합니다. 이는 애플리케이션의 번들 크기를 증가시킬 수 있습니다.
        
    - **Lodash-es:** 필요한 함수만 가져와 사용할 수 있기 때문에, 애플리케이션의 번들 크기를 최적화할 수 있습니다. 이는 특히 모듈 번들러를 사용하는 프로젝트에서 중요합니다.
        

따라서, `lodash-es`를 사용하는 이유는 주로 모듈 시스템의 지원과 번들 크기 최적화입니다. 프로젝트에서 필요한 부분만 선택적으로 가져와 사용함으로써, 필요하지 않은 기능을 배제하고 애플리케이션의 성능을 향상시킬 수 있습니다.

## lodash-es 예제

### Math :

#### add

#### ceil

#### divide

#### floor

#### max

#### maxBy

#### mean

#### meanBy

#### min

#### minBy

#### multiply

#### round

#### subtract

#### sum

#### sumBy


### Function :

after

ary 

before

bind

bindKey

curry

curryRight

debounce

defer

delay

flip

memoize

negate

once

overArgs

partial

partialRight

rearg

rest

spread

throttle

unary

wrap

### Object :

#### assign

#### extend

#### extendWith

#### assignWith

#### at

#### create

#### defaults

#### defaultsDeep

#### toParis

#### toPairsln

#### findKey

#### findLastKey

#### forIn

#### forInRight

#### forOwn

#### forOwnRight

#### forOwn

#### forOwnRight

#### functions

#### functionsIn

#### get

#### has

`has` 함수는 객체 내에 특정 경로에 속성이 존재하는지 여부를 확인하는 데 사용됩니다.

```
import { has } from 'lodash-es'; // 객체 정의 

const person = { 
	name: 'Alice', 
	details: { 
		age: 30, 
		address: { 
			city: 'New York', 
			country: 'USA' 
		} 
	} 
}; // 객체 내 특정 경로에 속성이 존재하는지 확인 

const hasCity = has(person, 'details.address.city'); 
console.log(hasCity); // 출력: true 

const hasJob = has(person, 'details.job'); 
console.log(hasJob); // 출력: false`
```


`Object.hasOwnProperty` vs Lodash `has`  
`Object.hasOwnProperty`와 Lodash의 `has` 함수는 모두 객체 내에 특정 속성이 존재하는지 확인하는 데 사용됩니다. 그러나 이 두 가지 방법에는 몇 가지 다른점이 있습니다.

1. **사용 방식:**
    
    - `Object.hasOwnProperty`: JavaScript의 기본 메서드로, 객체의 프로토타입 체인을 따라가지 않고 객체 자체에 속성이 존재하는지 여부를 확인합니다.
        
    - `_.has` (Lodash의 `has` 함수): Lodash 라이브러리의 메서드로, 객체의 특정 경로에 속성이 존재하는지 확인하며, 경로를 따라 내부 속성을 검색할 수 있습니다.
        
2. **경로 지정:**
    
    - `Object.hasOwnProperty`는 직접적으로 객체 내부의 속성을 확인하며, 프로토타입 체인을 따라가지 않습니다.
        
    - Lodash의 `has` 함수는 객체의 특정 경로에 속성이 존재하는지 확인하며, 중첩된 객체에서도 해당 경로를 따라 내부 속성을 검색할 수 있습니다. 
        
3. **안전한 사용:**
    
    - `Object.hasOwnProperty`를 사용할 때, 직접 속성을 확인하므로 프로토타입 체인으로부터의 상속된 속성을 확인하지 않습니다. 이는 원하는 동작이지만, 특정 상황에서는 부모 객체에서 상속받은 속성을 놓치는 경우가 있을 수 있습니다.
        
    - Lodash의 `has` 함수는 경로를 따라 내부 속성을 검색할 수 있어 중첩된 객체에서도 안전하게 속성을 확인할 수 있습니다.
        

따라서 `Object.hasOwnProperty`는 직접 속성을 확인하는 데에 사용되고, Lodash의 `has` 함수는 객체의 경로를 따라 속성을 확인하는 데에 사용됩니다. `has` 함수는 프로토타입 체인을 따라가며 안전하게 중첩된 속성을 확인할 수 있어 더 유연하게 사용할 수 있습니다.

#### hasIn

`hasIn` 함수는 객체의 프로토타입 체인을 따라 특정 경로에 속성이 존재하는지 여부를 확인합니다.

`hasIn` 함수는 객체의 프로토타입 체인을 따라 특정 경로에 속성이 존재하는지 확인합니다.

객체 내부뿐만 아니라 프로토타입 체인까지 확인하여 해당 경로에 속성이 있는 경우 `true`, 없는 경우 `false`를 반환합니다.

```
import { hasIn } from 'lodash-es'; // 객체 정의 

function Foo() { 
	this.a = 1; 
} 
Foo.prototype.b = 2; 

const obj = new Foo(); // 객체의 프로토타입 체인을 따라 특정 경로에 속성이 존재하는지 확인 

const hasA = hasIn(obj, 'a'); 
console.log(hasA); // 출력: true 

const hasB = hasIn(obj, 'b'); 
console.log(hasB); // 출력: true`
```

#### invert

#### invertBy

#### invoke

#### keys

#### keysIn

#### mapKeys

#### mapValues

#### merge

#### mergeWith

#### omit

#### omitBy

#### pick

#### pickBy

#### result

#### set

#### setWith

#### transform

#### unset

#### update

#### updateWith

#### values

#### valuesIn

### **Collection :**

#### countBy

`countBy` 함수는 배열 또는 객체의 요소들을 기준으로 각 요소의 출현 횟수를 세는 데 사용됩니다.

즉, 이 함수는 주어진 컬렉션(배열 또는 객체)에서 각 항목이 몇 번 나타나느지를 세어 객체로 반환합니다.

이 함수는 첫 번째 매개변수로 컬렉션(배열 또는 객체)을 받고, 두 번째 매개변수는 각 요소를 처리하는 함수로, 각 요소를 기준으로 카운트하고자 하는 특정 조건이나 변형을 지정할 수 있습니다.

```
import { countBy } from 'lodash-es'; 

const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3, 1, 2, 1]; 
const countedNumbers = countBy(numbers, (num) => num); console.log(countedNumbers); 
// 출력: { '1': 4, '2': 3, '3': 2, '4': 1, '5': 1, '6': 1, '7': 1, '8': 1, '9': 1, '10': 1 } 

const students = [ 
	{ id: 1, name: 'Alice', grade: 'A' }, 
	{ id: 2, name: 'Bob', grade: 'B' }, 
	{ id: 3, name: 'Charlie', grade: 'A' }, 
	{ id: 4, name: 'David', grade: 'C' }, 
	{ id: 5, name: 'Eva', grade: 'B' }, 
	{ id: 6, name: 'Frank', grade: 'A' }, 
	{ id: 7, name: 'Alice', grade: 'B' }, 
	{ id: 8, name: 'Bob', grade: 'C' }, 
	{ id: 9, name: 'Alice', grade: 'A' }, 
]; 

// 학생들의 학점(grade) 별 출현 횟수를 카운트합니다. 
const countedGrades = countBy(students, 'grade'); 
console.log(countedGrades); // 출력: { A: 4, B: 3, C: 2 } 

// 복잡한 데이터 객체 배열 (객체 안에 객체가 포함된 경우) 
const data = [ 
	{ id: 1, info: { category: 'A', value: 10 } }, 
	{ id: 2, info: { category: 'B', value: 20 } }, 
	{ id: 3, info: { category: 'A', value: 30 } }, 
	{ id: 4, info: { category: 'C', value: 40 } }, 
	{ id: 5, info: { category: 'B', value: 50 } }, 
	{ id: 6, info: { category: 'A', value: 60 } }, 
	{ id: 7, info: { category: 'B', value: 70 } }, 
	{ id: 8, info: { category: 'C', value: 80 } }, 
	{ id: 9, info: { category: 'A', value: 90 } }, 
]; 

// 'info' 객체 내 'category' 별 출현 횟수를 카운트합니다. 
const countedCategories = countBy(data, (item) => item.info.category); console.log(countedCategories); // 출력: { A: 4, B: 3, C: 2 }`