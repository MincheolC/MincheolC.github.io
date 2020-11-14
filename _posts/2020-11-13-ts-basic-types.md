---
title: 'Typescript Basic Types'
excerpt: 'Typescript에서 사용되는 기본 타입들'
categories:
  - typescript
toc: true
---

## Basic Types

<a name="boolean"></a>

### Boolean

```tsx
let isDone: boolean = false;
```

<a name="number"></a>

### Number

```tsx
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n;
```

### String

```tsx
let color: string = "blue";
color = 'red';
let sentence: string = `Hello, I like ${color}`.
```

### Array

```tsx
let list: number[] = [1, 2, 3];
let list2: Array<number> = [1, 2, 3];
```

### Tuple

```tsx
// Declare a tuple type
let x: [string, number];
x = ['hello', 10];
console.log(x[0]); // hello
console.log(x[1]); // 10
```

### Enum

값을 주면 값부터 증분. mapping된 numeric 값으로 이름 조회 가능.

```tsx
enum Color {
  Red = 1,
  Green,
  Blue,
}
const c: Color = Color.Green;
const colorName: string = Color[2];

console.log(c); // 2
console.log(colorName); // Green
```

### Unknown

애플리케이션을 개발할 때 **변수의 타입을 모르는 경우** 사용. 사용자 입력, API 값 등 동적으로 값을 할당하는 경우에 사용. type 검사를 통해 좀 더 구체화시킬 수 있음.

```tsx
let maybe: unknown = 4;
maybe = 'maybe string';

// type 검사 구체화
if (maybe === true) {
  const aBoolean: boolean = maybe;
} else if (typeof maybe === 'string') {
  const aString: string = maybe;
}
```

### Any

Typescript로 없이 작성된 코드와 같이 타입 정보를 사용할 수 없는 경우 any 타입으로 지정하여 검사를 opt-out시킬 수 있음.

**안전하지 않기 때문에 정말 필요한 경우가 아니면 사용하지 않는 것이 좋음. 컴파일은 정상으로 되나 런타임에서 에러 발생할 수 있음**.

```tsx
declare function getValue(key: string): any;
// 'getValue'의 리턴값 확인 하지 않음.
const str: string = getValue('myString');

let looselyTyped: any = {};

// Compile 정상, Runtime 에러 발생
let d = looselyTyped.a.b.c.d;
```

### Void

반환값이 없는 경우 사용.

```tsx
function warnUser(): void {
  console.log('This is my warning message');
}
```

### Null, Undefined

`void`처럼 그 자체로는 딱히 유용하지 않음. `null`과 `undefined`는 모든 타입의 하위 타입인데, `strictNullChecks` 플래그를 사용하면 `unknown`, `any`에만 할당 가능. 이것은 많은 일반적인 오류를 방지하는데 도움이 됨.

```tsx
let u: undefined = undefined;
let n: null = null;
```

### Never

절대 발생하지 않는 값을 나타냄.

```tsx
// 도달할 수 있는 끝 점이 없음.
function error(message: string): never {
  throw new Error(message);
}

function fail() {
  return error('Something failed');
}

function infiniteLoop(): never {
  while (true) {}
}
```

### Object

원시 타입이 아님.

> Generally, you won’t need to use this. [Typescript Docs]

```tsx
declare function create(o: object | null): void;

// OK
create({ prop: 0 });
create(null);
```

## Type assertions

Typescript보다 개발자가 값의 구체적인 타입을 잘 아는 상황이 있음. 그럴 때 컴파일러에게 "나를 믿어" 라고 알려주는 방법임. **런타임에 영향을 주지 않고 컴파일할 때만 사용 됨**.

```tsx
// as
let someValue: unknown = 'this is a string';

let lenAs: number = (someValue as string).length;
// or
let lenBracket: number = (<string>someValue).length;
```

## Number, String, Boolean, Symbol, Object

**lowercase만 타입으로 사용할 것.** `Number, String, Boolean, Symbol, Object` 은 원시타입을 참조하지 않기 떄문에 유형으로 사용해서는 안 됨.

### References

- [Typescript Handbook Basic Types](https://www.typescriptlang.org/docs/handbook/basic-types.html)
