---
title: 'Typescript Functions'
excerpt: 'Typescript 공식 handbook의 Functions를 정리한 글'
categories:
  - typescript
toc: true
---

이 글은 Typescript 공식 Handbook에서 Functions 편을 정리 한 글입니다.

Javascript처럼 명명 함수 또는 익명 함수로 생성할 수 있다.

```tsx
// Named function
function add(x, y) {
  return x + y;
}

// Anonymous function
let myAdd = function (x, y) {
  return x + y;
};
```

## Function Types

### Typing the function

```tsx
function add(x: number, y: number): number {
  return x + y;
}

let myAdd = function (x: number, y: number): number {
  return x + y;
};
```

Typescript는 return 문을 보고 반환 타입을 파악할 수 있어, 선택적으로 반환 타입을 생략할 수 있다.

### Writing the function type

Function types는 인수 타입과 반환 타입 두 개를 가지고 있다. Function types를 정의할 때, 이 두 개는 필수로 필요하다. 매개변수 타입들을 각각 이름과 타입으로 파라미터 목록처럼 적는데, 이는 가독성을 올려준다.

```tsx
let myAdd: (baseValue: number, increment: number) => number = function (
  x: number,
  y: number,
): number {
  return x + y;
};
```

파라미터 타입과 반환 타입을 `=>` 를 사이에 사용하여 구분한다. 만약에 반환 타입이 없는 경우에 `void`를 반환 타입으로 적어준다.

매개 변수와 반환 타입만 함수 타입을 구성하고 캡처된 타입(함수 외부 변수)는 모든 함수의 "숨겨진 상태"의 일부이다.

### Inferring the types (타입 추론)

한 쪽만 타입이 정의되어 있으면 Typescript가 알아서 타입을 추론하는데 이를 contextual typing이라 한다.

```tsx
let myAdd = function (x: number, y: number): number {
  return x + y;
};

let myAdd2: (baseValue: number, increment: number) => number = function (x, y) {
  return x + y;
};
```

### Optional and Default Parameters

Typescript는 기본적으로 function이 호출될 때 모든 매개변수가 필수여야 한다고 여긴다. 즉, `null`과 `undefined`도 허용되지 않는다.

```tsx
function buildName(firstName: string, lastName: string) {
  return firstName + ' ' + lastName;
}

let result1 = buildName('Bob'); // ERROR, too few parameters
let result2 = buildName('Bob', 'Adams', 'Sr.'); // ERROR, too many parameters
let result3 = buildName('Bob', 'Adams'); // ah, just right
```

Javascript의 경우 각 매개변수는 optional이기에, 매개변수가 주어지지 않으면 `undefined`를 할당한다. Typescript에서도 해당 매겨변수 끝에 `?`를 추가하여 optional을 구현할 수 있다.

```tsx
function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + ' ' + lastName;
  else return firstName;
}

let result1 = buildName('Bob'); // works correctly now
let result2 = buildName('Bob', 'Adams', 'Sr.'); // ERROR, too many parameters
let result3 = buildName('Bob', 'Adams'); // ah, just right
```

**모든 optional 매개변수는 필수 매개변수 뒤에 와야한다**.

매개변수가 주어지지 않거나 `undefined`인 경우에 사용할 default-initialized 값을 설정할 수 있다.

```tsx
function buildName(firstName: string, lastName = 'Smith') {
  return firstName + ' ' + lastName;
}

let result1 = buildName('Bob'); // works correctly now, returns "Bob Smith"
let result2 = buildName('Bob', undefined); // still works, also returns "Bob Smith"
```

**default-initialized 매개변수는 optional과 달리 필수 매개변수 뒤에 올 필요가 없다. 대신, 필수 매개변수 전에 default-initialized 매개변수가 오는 경우 유저가 `undefined`를 명시적으로 넘겨줘야 한다.**

```tsx
function buildName(firstName = 'Will', lastName: string) {
  return firstName + ' ' + lastName;
}

let result1 = buildName('Bob'); // ERROR, too few parameters
let result2 = buildName('Bob', 'Adams', 'Sr.'); // ERROR, too many parameters
let result3 = buildName('Bob', 'Adams'); // okay and returns "Bob Adams"
let result4 = buildName(undefined, 'Adams'); // okay and returns "Will Adams"
```

### Rest Parameters

여러 매개 변수를 하나의 그룹으로 사용하거나, 함수가 궁극적으로 사용할 매개변수 수를 모를 때 사용한다. Rest parameters는 무한 개의 optional 매개변수로 처리된다. 매개변수 앞에 `...`를 붙이면 된다.

```tsx
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + ' ' + restOfName.join(' ');
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;

// employeeName will be "Joseph Samuel Lucas MacKinzie"
let employeeName = buildName('Joseph', 'Samuel', 'Lucas', 'MacKinzie');
```

## This

[Understanding JavaScript Function Invocation and "this"](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)

Typescript는 Javascript의 상위 집합이므로 this의 기능을 사용하는 방법과 잘못된 사용을 파악하는 방법을 배워야한다. Typescript는 몇 가지 기술을 사용하여 this의 잘못된 사용을 알려준다.

### This and Arrow functions

Javascript는 함수가 호출될 때 this를 같이 바인드시킨다. 이것은 매우 유용한 기능이지만 함수가 실행되고 있는 context(문맥)에 대해 항상 알아야하는 대가가 따른다. 특히 함수를 반환하거나 함수를 인수로 전달할 때 매우 혼동된다.

```tsx
let deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function () {
    return function () {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit); // ERROR
```

`this`가 deck 객체가 아닌 window를 가르키면서 발생하는 오류다. 이는 cardPicker()를 자체적으로 호출하기 때문이다. ECMAScript 6 arrow function을 사용하면 이 문제점을 해결할 수 있다. **Arrow function은 호출된 곳이 아닌 생성된 곳을 this로 capture하기 때문이다**.

```tsx
let deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function () {
    // Arrow Function 사용
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit);
```

`--noImplicitThis` 플래그를 컴파일러에게 넘기주면 Typescript가 `this`에 관한 실수를 찾아 경고해준다. 이것은 `this.suits[pickedSuit]`의 `this`가 `any` 타입이라는 것을 지적한다.

### this parameter

`this.suits[pickedSuit]`의 타입이 `any`인 이유는 `this`가 객체 리터럴 안의 함수 표현식에서 비롯되기 때문이다. 이를 수정하기 위해, 명시적인 `this` 매개변수를 제공할 수 있다. `this` 매개변수는 함수의 매개변수 목록 중 가장 앞에 오는 가짜 매개변수이다.

```tsx
interface Card {
  suit: string;
  card: number;
}

interface Deck {
  suits: string[];
  cards: number[];
  createCardPicker(this: Deck): () => Card; // <--
}

let deck: Deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function (this: Deck) {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit);
```

### this parameter in callbacks

callback 함수를 라이브러리에 전달하는 경우에 `this` 오류가 발생할 수 있다. 라이브러리가 callback함수를 일반 함수처럼 호출하는 경우, `this`는 `undefined` 이기 때문이다. 일부 작업을 통해 이런 callback 오류를 방지할 수 있다. 먼저, 라이브러리 작성자는 다음과 같이 `this` 와 함께 callback type을 annotate해야 한다.

```tsx
interface UIElement {
  addClickListener(onclick: (this: void, e: Event) => void): void;
}
```

`this: void`는 `onclick` 함수는 this 타입이 필요없다는 의미다. 두번째로, `this`와 함께 코드가 호출됨을 annotate해야 한다.

```tsx
class Handler {
  info: string;
  onClickBad(this: Handler, e: Event) {
    // oops, used `this` here. using this callback would crash at runtime
    this.info = e.message;
  }
}

let h = new Handler();
uiElement.addClickListener(h.onClickBad);
```

`this`가 `Handler` 인스턴스여야 한다고 명시적으로 annotate하면 Typescript는 addClickListener가 `this: void`를 필요로한다는 걸 감지하고 오류를 발생시킨다. 오류를 고치기 위해 `this`의 타입을 변경하면 onClickGood에서 `this`가 `void`이기 때문에 `this`를 사용하지 못한다.

```tsx
class Handler {
  info: string;
  onClickGood(this: void, e: Event) {
    // can't use `this` here because it's of type void!
    console.log('clicked!');
  }
}

let h = new Handler();
uiElement.addClickListener(h.onClickGood);
```

만약 `this: void`를 유지하면서 `this`를 사용하고 싶다면 arrow function을 사용해야한다.

```tsx
class Handler {
  info: string;
  onClickGood = (e: Event) => {
    this.info = e.message;
  };
}
```

단점은 Handler 타입의 객체 당 하나의 arrow function가 생성된다는 것이다. 반면에, 메서드는 한 번만 생성되고 Handler의 프로토타입에 첨부되고 Handler 타입의 모든 객체 간에 공유된다.

## Overloads

Javascript는 본질적으로 매우 동적인 언어다. 단일 javascript 함수가 전달 된 인수의 형태에 따라 다른 타입의 객체를 반환하는 건 일반적이다.

```tsx
let suits = ['hearts', 'spades', 'clubs', 'diamonds'];

function pickCard(x: any): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == 'object') {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == 'number') {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [
  { suit: 'diamonds', card: 2 },
  { suit: 'spades', card: 10 },
  { suit: 'hearts', card: 4 },
];

let pickedCard1 = myDeck[pickCard(myDeck)];
alert('card: ' + pickedCard1.card + ' of ' + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert('card: ' + pickedCard2.card + ' of ' + pickedCard2.suit);
```

이것을 타입이 있는 시스템으로 표현하려면 어떻게 해야할까?

답은 동일한 함수에 대해 여러 함수 타입(**overloads 목록**)을 제공하는 것이다. `function pickCard(x): any`는 overload가 아니다.

```tsx
let suits = ['hearts', 'spades', 'clubs', 'diamonds'];

function pickCard(x: { suit: string; card: number }[]): number;
function pickCard(x: number): { suit: string; card: number };
function pickCard(x: any): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == 'object') {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == 'number') {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [
  { suit: 'diamonds', card: 2 },
  { suit: 'spades', card: 10 },
  { suit: 'hearts', card: 4 },
];

let pickedCard1 = myDeck[pickCard(myDeck)];
alert('card: ' + pickedCard1.card + ' of ' + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert('card: ' + pickedCard2.card + ' of ' + pickedCard2.suit);
```

컴파일러가 맞는 타입을 고르기 위해, 첫 overload부터 순서대로 제공된 인수로 함수 호출을 시도한다. 그리고 만약에 일치하는 것을 찾으면 해당 overload가 올바르다고 판단한다. 이러한 이유로, **가장 구체적인 것부터 가장 덜 구체적인 순으로 overload를 작성하는 것이 일반**적이다.

## References

- [Typescript Handbook Functions](https://www.typescriptlang.org/docs/handbook/functions.html)
