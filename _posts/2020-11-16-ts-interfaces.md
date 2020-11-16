---
title: 'Typescript Interfaces'
excerpt: 'Typescript 공식 handbook의 Interface를 정리한 글'
categories:
  - typescript
toc: true
---

이 글은 Typescript 공식 Handbook에서 Interfaces 편을 정리 한 글입니다.

## Interfaces

Typescript의 핵심 원칙 중 하나는 type checking이 값의 모형(shape)에 초점을 맞춘다는 것이다. 이를 `Duck typing` or `structural subtyping` 이라고도 한다. Typecript의 Interfaces는 **type들의 이름을 지정하는 역할**을 수행함. **코드 내외로 약속(contract)을 정의**하는 강력한 방법이다.

```tsx
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
  // console.log(labeledObj.size); // compile ERROR
}

const obj = { a: 1, label: 'One' };
printLabel(obj); // One
```

Type-checker는 함수로 전달된 객체에서 실제로는 더 많은 property가 있을 수 있지만 **필요한(required) 값만 type-checking을 하고 allow한다**.

### Optional Properties

Interface의 모든 property가 필수(required)일 필요는 없다. `Optional Properties`**는 'options bags'와 같은 패턴(몇 가지 프로퍼티만 채운 객체를 함수에 넘기는 방법)을 만들 때 많이 사용 된다**.

Optional Properties는 Interface의 property를 선언 시 끝에 `?`만 붙여 주면 된다.

```tsx
interface SquareConfig {
  color?: string; // <--
  width?: number; // <--
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  const newSquare = { color: 'white', area: 100 };
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}
let mySquare = createSquare({ color: 'black' });
```

### Readonly properties

객체가 처음 생성 될 때만 수정가능한 property들을 위한 기능이다.

```tsx
interface Point {
  readonly x: number;
  readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
p1.x = 5; // ERROR!
```

`Array<T>`와 동일한 타입이지만 수정 가능한 메소드가 제공되지 않는 `ReadonlyArray<T>`가 있다.

```tsx
const a: number[] = [1, 2, 3, 4];
let b: number[];
const ro: ReadonlyArray<number> = a;

a[1] = 3; // Error
b = ro; // Error, The type 'readonly number[]' is 'readonly' and cannot be assigned to the mutable type 'number[]'.

b = ro as number[]; // Type Assertion을 이용해서 Override 가능
b[1] = 3;
```

`const`는 변수(variable)에 사용하고 `readonly`는 속성(property)에 사용한다.

### Excess Property Checks

Optional properties(Option bags)을 사용할 때 정의되지 않은 properties를 넘기는 경우 typescript는 bug로 간주한다.

```tsx
mySquare = createSquare({ colur: 'red', width: 100 }); // Error
// '{ colur: string; width: number; }' 형식의 인수는 'SquareConfig' 형식의 매개 변수에 할당될 수 없습니다.
```

(Solution 1) `Type Assertion`을 사용해서 해결 가능하다.

```tsx
mySquare = createSquare({ colur: 'red', width: 100 } as SquareConfig);
```

(Solution 2) **만약 추가 property가 생길 것이 확실하면 `index signature`를 추가하는 것이 더 좋은 방안이다**.

```tsx
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any; // <--
}
```

(Solution 3) 다른 obj에 할당한 후에 넘기기. 초과된 프로퍼티를 체크하지 않는다. (단, 공통 객체 property가 없는 경우에는 Error)

```tsx
const obj = { colur: 'red', width: 100 };
mySquare = createSquare(obj);
```

**위와 같이 Check를 피하는 것은 왠만하면 하지 말자. 대부분 버그일 확률이 높다. optional properties을 사용했을 때 초과 속성 검사 문제가 발생한다면 속성을 수정하거나, 타입 선언을 수정하는 것이 바람직하다.**

### Function Types

Interface는 Javascript 객체가 취할 수 있는 다양한 형태를 설명할 수 있음. 또한 속성을 통해 객체를 설명할 뿐만 아니라, function types도 설명할 수 있다.

Function types을 설명하기 위해, Call Signature를 interface에 제공해야 한다.

```tsx
interface FtName {
  (PARAMETER: PARAM_TYPE): RETURN_TYPE; // Call signature
}
```

Function types을 정의한 뒤에, 다른 인터페이스처럼 사용할 수 있음. 또한 파라미터 이름이 Function Type에 정의된 것과 일치할 필요는 없다.

```tsx
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;

mySearch = function (src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
};
```

한 번에 하나씩 서로 대응되는 위치에 있는 인자를 체크한다. 타입을 지정하지 않아도, 함수가 SearchFunc 타입에 직접 할당되기 때문에 Typescript의 contextual typing이 타입을 유추할 수 있다.

```tsx
let mySearch: SearchFunc;

mySearch = function (src, sub) {
  let result = src.search(sub);
  return result > -1;
};
```

### Indexable Types

`a[10]` 또는 `ageMap["daniel"]`과 같이 "인덱싱" 할 수 있는 타입도 설명할 수 있다. Indexable types는 객체를 인덱싱할 때 사용할 수 있는 타입과 반환 타입을 설명하는 Index signature가 있다.

```tsx
interface INAME {
  [INDEXER_NAME: INDEXER_TYPE]: RETURN_TYPE; // Index signature
}
```

```tsx
interface Items {
  [index: number]: string;
}

let myItems: Items;
myItems = ['Bob', 'Fred'];

let myStr: string = myItems[0];
```

Indexing 결과의 반환 타입으로 유니온 쓰기

```tsx
interface Items {
  [index: number]: string | boolean | number[];
}

let myItems: Items;
myItems = ["Bob", "Fred", true, [1,2,3];

console.log(myItems[0]);   // "Bob"
console.log(myItems[2]);   // true
console.log(myItems[3]);   // [1,2,3]
```

Index signature은 `number`와 `string` 타입을 지원한다. **두 타입의 인덱서를 모두 지원할 수 있지만 숫자 인덱서에서 반환 된 타입은 문자열 인덱서에서 반환된 타입의 하위 타입이어야 한다**. (`number`로 인덱싱 하는 경우, javascript가 인덱싱 전에 `string`으로 변환하기 때문이다;`100`으로 인덱싱 = `"100"`으로 인덱싱)

```tsx
// 상위 타입
interface Animal {
  name: string;
}

// 하위 타입
interface Dog extends Animal {
  breed: string;
}

interface NotOkay {
  [x: number]: Animal; // 숫자 인덱스 형식 Animal을 문자 인덱스 형식 Dog에 할당할 수 없음.
  [x: string]: Dog;
}

interface Okay {
  [x: number]: Dog;
  [x: string]: Animal;
}
```

Index signature는 "dictionary" 패턴을 설명하는 강력한 방법이지만 모든 속성들이 반환 타입이 일치하도록 강제한다. 이는 문자열 인덱스가 `obj.property`를 `obj["property"]`로도 사용할 수 있음을 선언하기 때문이다.

```tsx
interface NumberDictionary {
  [index: string]: number;
  length: number; // ok, length is a number
  name: string; // error, the type of 'name' is not a subtype of the indexer
}

// solution union 사용
interface NumberDictionary {
  [index: string]: number | string;
  length: number;
  name: string;
}
```

Index signature도 `readonly`를 적용할 수 있다.

```tsx
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let myArray: ReadonlyStringArray = ['Alice', 'Bob'];
myArray[2] = 'Mallory'; // error!
```

### Class Types

**Interface 구현(implement)**

C#, Java같은 언어의 일반적인 interface 사용은 class에게 특정 contract를 준수하도록 하는 것인데 Typescript에서도 가능하다.

```tsx
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```

Interface는 class의 public 측면만을 설명함으로써, 클래스의 private한 특정 타입 부분을 확인하는 것을 금지한다.

**Class의 static과 instance의 차이점**

Class와 Interface로 작업할 때는, class에는 static 타입과 instance 타입이 있다는 것을 명심해라. Contruct signature를 이용해서 interface를 만든 뒤 이 interface를 구현하는 class를 만들려고 하면 오류가 발생한다.

```tsx
interface ClockConstructor {
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
  // Error
  currentTime: Date;
  constructor(h: number, m: number) {}
}
```

Class가 interface를 구현할 때, constructor는 static 부분에 있는데, instance 부분만 확인하기 때문에 발생하는 오류다. 대신 class의 static 부분을 직접 작업해야한다.

```tsx
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log('beep beep');
  }
}

class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log('tick tock');
  }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
Try;
```

좀 더 간단한 방법은 다음과 같다.

```tsx
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log('beep beep');
  }
};
```

**Interface 확장(extend)**

Class처럼 Interface도 서로 확장(extend) 가능함. 이를 통해 interface간 member 복사가 가능하여, interface를 좀 더 재사용 가능한 단위로 분리할 수 있게 한다.

```tsx
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = {} as Square;
square.color = 'blue';
square.sideLength = 10;
square.penWidth = 5.0;
```

### Class를 확장하는 Interface

Interface가 class를 확장하는 경우 구현은 제외하고 member(private, protected 포함)만 상속 받는다. 즉, private 또는 protected member로 class를 확장하는 interface를 만들 때 해당 interface 타입은 해당 class 또는 sub class에 의해서만 구현 될 수 있다.

**이는 많은 상속 계층이 있지만, 특정 속성을 가진 sub class에서만 동작하도록 지정하려고 할 때 유용하다**.

```tsx
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control implements SelectableControl {
  select() {}
}

class TextBox extends Control {
  select() {}
}

class ImageControl implements SelectableControl {
  // Error
  private state: any;
  select() {}
}
```

## References

- [Typescript Handbook Interfaces](https://www.typescriptlang.org/docs/handbook/interfaces.html)
