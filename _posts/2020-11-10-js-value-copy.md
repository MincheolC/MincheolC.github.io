---
title: '[JS] 데이터 타입 (Data Types)'
excerpt: 'JS의 데이터 타입 종류와 특징'
categories:
  - javascript
---

Javascript의 데이터 타입(Data type)은 원시 자료형(Primitives)과 객체(Object)로 나뉨.

## 원시 자료형 (Primitives)

원시 자료형이란 객체가 아니면서 메서드도 가지지 않는 데이터를 말함. 아래와 같이 6종류로 구성됨.

- number
- string
- bigint
- boolean
- undefined
- symbol

모든 원시 값은 변형할 수 없음.

```js
const str = 'hello';
console.log(str); // hello

str.toUpperCase();
console.log(str); // hello
```

원시 값을 새로운 변수에 복사하면 새로운 메모리 영역에 값이 복사됨.

```js
const one = 1;
let num = one;
num = 2;
console.log(one); // 1
console.log(num); // 2
```

**Q.** 분명 원시 자료형은 객체가 아니고 메서드도 가지지 않는다 했는데 왜 메소드를 사용할 수 있지?

JS에는 undefined와 null을 제와하고 원시 래퍼 객체가 존재하는데, 원시값에 메소드를 호출하면 JS가 자동적으로 아래의 원시 래퍼 객체로 boxing해서 해당 객체에 구현되어있는 메소드를 호출해주기 때문입니다.

> In contexts where a method is to be invoked on a primitive string or a property lookup occurs, JavaScript will automatically wrap the string primitive and call the method or perform the property lookup. - MDN -

- String
- Number
- Boolean
- BigInt
- Symbol

```js
const str = 'hello';
console.log(str.toUpperCase()); // 'HELLO'
```

## 객체 (Object)

객체는 속성(property)들을 담고있는 가방(collection) 으로 볼 수 있음. 다음과 같은 방법으로 객체를 생성할 수 있음.

```js
let obj = new Object(); // {}
obj = Object.create(Object.prototype); // {}
obj = {}; // {}
```

JS에는 다양한 [표준 내장 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects)가 있음. (`Array`, `Map`, `Math`, etc)

객체는 변수가 원시값과 달리 참조값을 가르킴. 즉, 객체의 주소를 가르키고 있기 때문에 새로운 변수에 할당 후 변경하더라도 원본 값이 변경됨.

```js
const obj = {
  a: 1,
};
const obj2 = obj;
obj2.a = 2;

console.log(obj); // 2
console.log(obj); // 2
```

### 얕은 복사 (Shallow Copy)

객체의 참조 주소만 복사하는 것. `obj.a.b`와 `obj.c`의 **변화를 주의있게 보고 차이를 생각해보길 바람.**

```js
const obj = {
  a: {
    b: 1,
  },
  c: 3,
};

// 전개 연산자를 이용한 복사
let o1 = { ...obj };
o1.a.b = 2;
o1.c = 1;

// Object.assign()을 이용한 복사
let o2 = Object.assign({}, obj);
o2.a.b = 3;

console.log(obj); // {a: { b: 3 }, c: 3}
console.log(o1); // {a: { b: 3 }, c: 1} <--
console.log(o2); // {a: { b: 3 }, c: 3}
```

### 깊은 복사 (Deep Copy)

객체의 모든 필드를 복사하여 새로운 메모리에 할당하는 것.

```js
const _ = require('lodash');

const obj = {
  a: {
    b: 1,
  },
  c: 3,
};

// 1. 재귀 함수 이용. (구현 생략)

// 2. JSON 객체 사용.
let o1 = JSON.parse(JSON.stringify(obj));
o1.a.b = 2;
o1.c = 1;
// 3. lodash (외부 라이브러리) 이용
let o2 = _.cloneDeep(obj);
o2.a.b = 3;

console.log(obj); // {a: { b: 1 }, c: 3}
console.log(o1); // {a: { b: 2 }, c: 1}
console.log(o2); // {a: { b: 3 }, c: 3}
```

> JSON 객체는 재귀함수와 lodash에 비해 속도가 느리다고 함. -JavaScript로 Deep Copy 하는 여러 방법-

### References

- [MDN Primitive](https://developer.mozilla.org/ko/docs/Glossary/Primitive)
- [MDN String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
- [MDN Object.create](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
- [MDN Standard built-in objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)
- [Understanding Deep and Shallow Copy in Javascript](https://medium.com/@manjuladube/understanding-deep-and-shallow-copy-in-javascript-13438bad941c)
- [Javascript로 Deep Copy하는 여러 방법](https://chaewonkong.github.io/posts/js-deep-copy.html)
