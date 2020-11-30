---
title: 'Typescript Literal Types'
excerpt: 'Typescript 공식 handbook의 Literal Types를 정리한 글'
categories:
  - typescript
toc: true
---

이 글은 Typescript 공식 Handbook에서 Literal Types 편을 정리 한 글입니다.

리터럴(Literal)은 collective 타입의 좀 더 구체적인 하위 타입이다. 이것이 의미하는 바는 "Hello World"는 `string`이지만 `string`은 "Hello World"가 아니라는 것이다.

현재 Typescript에는 `number`, `string`, `boolean` 세 개의 리터럴 타입을 사용할 수 있다.

## Literal Narrowing

```tsx
// const로 정의한 helloWorld는 절대 변할 일이 없기 때문에,
// Typescript는 string이 아닌 "Hello World" 타입으로 정한다.
const helloWorld = 'Hello World';

// 반면에 let은 수정이 가능하기 때문에 string으로 선언한다.
let hiWorld = 'Hi World';
```

무한한 경우의 수에서 유한한 경우의 수로 줄이는 과정을 `narrowing`이라고 한다.

## String Literal Types

string 리터럴 타입은 `union types, type guards, type aliases`와 잘 조합된다. 이러한 기능을 함께 사용하여 string으로 enum처럼 동작하게 할 수 있다.

```tsx
type Easing = 'ease-in' | 'ease-out' | 'ease-in-out';

class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === 'ease-in') {
      // ...
    } else if (easing === 'ease-out') {
    } else if (easing === 'ease-in-out') {
    } else {
      // It's possible that someone could reach this
      // by ignoring your types though.
    }
  }
}

let button = new UIElement();
button.animate(0, 0, 'ease-in');
button.animate(0, 0, 'uneasy'); // ERROR
```

또한 overload들을 구분하는 용으로도 사용할 수 있다.

```tsx
function createElement(tagName: 'img'): HTMLImageElement;
function createElement(tagName: 'input'): HTMLInputElement;
// ... more overloads ...
function createElement(tagName: string): Element {
  // ... code goes here ...
}
```

## Numeric Literal Types

numeric 리터럴 타입도 string 리터럴 타입처럼 동작한다.

```tsx
function rollDice(): 1 | 2 | 3 | 4 | 5 | 6 {
  return (Math.floor(Math.random() * 6) + 1) as 1 | 2 | 3 | 4 | 5 | 6;
}

const result = rollDice();
```

일반적인 사용 사례는 config 값을 설명하는 것입니다.

```tsx
interface MapConfig {
  lng: number;
  lat: number;
  tileSize: 8 | 16 | 32;
}

setupMap({ lng: -73.935242, lat: 40.73061, tileSize: 16 });
```

## Boolean Literal Types

boolean 리터럴 타입을 사용하여 프로퍼티들이 관련된 객체 값들을 제한할 수 있다.

```tsx
interface ValidationSuccess {
  isValid: true;
  reason: null;
}

interface ValidationFailure {
  isValid: false;
  reason: string;
}

type ValidationResult = ValidationSuccess | ValidationFailure;
```

## References

- [Typescript Handbook Literal Types](https://www.typescriptlang.org/docs/handbook/literal-types.html)
