---
title: 'Typescript Classes'
excerpt: 'Typescript 공식 handbook의 Classes를 정리한 글'
categories:
  - typescript
toc: true
---

이 글은 Typescript 공식 Handbook에서 Classes 편을 정리 한 글입니다.

ES6 이전의 Javascript는 컴포넌트를 재사용하기 위해 function과 prototype-based 상속을 사용했다. 그러나 이것은 객체지향 방식(클래스 상속)에 더 익숙해진 개발자들에게 어색함을 안겨주었다. Typescript에서는 개발자가 Javascript의 다음 버번을 기다릴 필요없이 이러한 기술(클래스)을 사용할 수 있다.

## Classes

다음은 `greeting` 변수, `constructor`, `greet` 메소드 이 3개의 멤버로 구성된 Greeter란 클래스다. 클래스의 멤버를 참조하기 위해 `this`를 사용한다.

```tsx
class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter = new Greeter("world");
```

마지막에 `new` 를 사용하여 Greeter 인스턴스를 생성했다. 이는 앞서 정의한 생성자를 호출하여 Greeter 형태의 새로운 객체를 만들고 초기화한다.

## Inheritance

Typescript에서는 일반적인 객체지향 패턴(OOP)을 사용할 수 있다. 가장 기본적인 패턴 중 하나는 상속을 사용하여 기존 클래스를 확장해 새 클래스를 만드는 것이다.

```tsx
class Animal {
  move(distanceInMeters: number = 0) {
    console.log(`Animal moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

여기서 Dog은 Animal에서 파생되는 파생클래스(derived class)이다. 종종 파생 클래스는 **서브클래스(sub class)**라고 하고 기본 클래스는 **수퍼클래스(super class)**라고 불린다.

좀 더 복잡한 예제를 보자.

```tsx
class Animal {
  name: string;
  constructor(theName: string) {
    this.name = theName;
  }
  move(distanceInMeters: number = 0) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

class Snake extends Animal {
  constructor(name: string) {
    super(name); // <--
  }
  move(distanceInMeters = 5) {
    console.log("Slithering...");
    super.move(distanceInMeters);
  }
}

class Horse extends Animal {
  constructor(name: string) {
    super(name); // <--
  }
  move(distanceInMeters = 45) {
    console.log("Galloping...");
    super.move(distanceInMeters);
  }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

각 파생클래스의 생성자는 **반드시 기본 클래스의 생성자를 실행하는 `super()`를 호출**해야한다. contructor body에서 `this`에 접근하기 전에 `super()`를 먼저 호출해야한다. 예제의 파생클래스들은 기본클래스의 `move`를 override하고 있다.

## Public, private, and protected modifiers

### Public by default

Typescript에서는 각 멤버들은 기본적으로 `public`이다.

### ECMAScript Private Fields

Typescript 3.8부터는 Javascript의 `private` 필드를 위한 새로운 구문(`#`)을 지원한다.

```tsx
class Animal {
  #name: string;  // <--
  constructor(theName: string) {
    this.#name = theName;
  }
}

new Animal("Cat").#name; // ERROR
```

### Understanding Typescript's private

Typescript는 자체적으로 `private`이라는 키워드를 지원한다.

```tsx
class Animal {
  private name: string; // <--

  constructor(theName: string) {
    this.name = theName;
  }
}

new Animal("Cat").name; // ERROR
```

**Typescript는 구조적 타입 시스템(structural type system)이다. 즉, 두 가지 타입을 비교할 때 근원에 관계 없이 모든 구성원의 타입이 호환되면 타입 자체가 호환된다고 말한다**. 그러나 private과 protected 멤버가 있는 타입을 비교할 때 이러한 타입을 다르게 취급한다. **만약 둘이 호환되려면 둘 중 하나에 private 멤버가 있는 경우, 다른 타입에는 동일한 선언에서 시작된 private 맴버가 있어야 한다.**

```tsx
class Animal {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

class Rhino extends Animal {
  constructor() {
    super("Rhino");
  }
}

class Employee {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // ERROR
```

### Understanding protected

protected modifier는 `protected`로 선언 된 멤버가 파생클래스 내에서 `this`로 엑세스 할 수 있다는 점을 제외하면 private modifier와 유사하다.

```tsx
class Person {
  protected name: string;
  protected constructor(name: string) {
    this.name = name;
  }
}

class Employee extends Person {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`;
  }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // ERROR
let john = new Person("John"); // ERROR
```

constructor 또한 `protected`로 마킹될 수 있는데, 이것은 클래스를 포함하는 클래스 외부에서 인스턴스화할 수 없지만 확장은 할 수 있음을 의미한다.

## Readonly modifier

멤버 변수를 readonly로 선언할 수 있다. readonly는 반드시 선언 또는 contructor에서 초기화해야한다.

```tsx
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8; // 선언

  constructor(theName: string) {
    this.name = theName; // 생성자에서 초기화
  }
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit";  // ERROR
```

## Parameter properties

매개 변수 속성을 사용하면 한 곳에서 멤버를 만들고 초기화 할 수 있다. 즉, **선언과 할당을 한번에 할 수 있다**.

`readonly` 뿐만 아니라 `private`, `public`, `protected`도 매개 변수 속성에 사용하면 해당 변수를 선언하고 초기화한다.

```tsx
class Octopus {
  readonly numberOfLegs: number = 8;
  constructor(readonly name: string) {} // <-- theName 없어지고 축약됨.
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name;
```

## Accessors

Typescript는 객체 멤버에 대한 엑세스를 가로채는 방법으로 **getter**와 **setter**를 지원한다. **이를 통해 각 객체에서 멤버에 엑세스하는 방법을 보다 세밀하게 제어할 수 있다**.

다음은 **setter**에 뒷단의 database 필드의 max-length와 호환되는지 `newName`의 길이를 확인하는 로직을 추가한 예제다.

```tsx
const fullNameMaxLength = 10;

class Employee {
  private _fullName: string = "";

  get fullName(): string {
    return this._fullName;
  }

  set fullName(newName: string) {
    if (newName && newName.length > fullNameMaxLength) {
      throw new Error("fullName has a max length of " + fullNameMaxLength);
    }

    this._fullName = newName;
  }
}

let employee = new Employee();
employee.fullName = "Bob Smith";

if (employee.fullName) {
  console.log(employee.fullName);
}
```

**접근자(Accessor)사용시 유의할 점**

1. ECMAScript 5 이상을 출력하도록 컴파일러 설정 필요.
2. `get`은 있는데 `set`이 없는 경우 자동으로 readonly로 유추된다. `.d.ts` 파일(타입 추론을 돕는 파일)을 생성할 때 유용

## Static properties

인스턴스가 아닌 클래스 자체에서 볼 수 있는 멤버이다. `static` 으로 마킹하면 되며, 클래스 이름을 통해 접근할 수 있다.

```tsx
class Grid {
  static origin = { x: 0, y: 0 };

  calculateDistanceFromOrigin(point: { x: number; y: number }) {
    let xDist = point.x - Grid.origin.x; // <--
    let yDist = point.y - Grid.origin.y; // <--
    return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
  }

  constructor(public scale: number) {}
}

let grid1 = new Grid(1.0); // 1x scale
let grid2 = new Grid(5.0); // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));
console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }));
```

## Abstract Classes

추상 클래스는 파생 될 수 있는 기본 클래스로 **직접 초기화될 수 없다**. 인터페이스(interface)와는 다르게 멤버에 대한 구현 세부 정보가 포함될 수 있다. `abstract` 키워드는 추상 클래스 뿐만 아니라 추상 메서드를 정의하는데 사용된다. `abstract`로 표시된 메서드는 구현을 포함하지 않으며, 파생 클래스에서 구현되어야 한다.

```tsx
abstract class Department {
  constructor(public name: string) {}

  printName(): void {
    console.log("Department name: " + this.name);
  }

  abstract printMeeting(): void; // must be implemented in derived classes
}

class AccountingDepartment extends Department {
  constructor() {
    super("Accounting and Auditing"); // constructors in derived classes must call super()
  }

  printMeeting(): void {
    console.log("The Accounting Department meets each Monday at 10am.");
  }

  generateReports(): void {
    console.log("Generating accounting reports...");
  }
}

let department: Department;
department = new Department(); // ERROR: cannot create an instance of an abstract class

department = new AccountingDepartment();
department.printName();
department.printMeeting();
department.generateReports(); // ERROR: Property 'generateReports' does not exist on type 'Department'.
```

## Advanced Techniques

### Constructor functions

Typescript에서 클래스를 선언하면 실제로 동시에 여러 선언을 생성하게 된다. 첫번째는 **클래스의 인스턴스 타입**이다. `let greeter: Greeter`는 greeter를 Greeter 클래스의 인스턴스로 사용하겠다는 의미다.

```tsx
class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world"
```

두번째로 **contructor functions**이라고 불리는 또 다른 값을 생성한다. 이 함수는 `new`로 클래스의 인스턴스를 생성할 때 호출된다. 다음은 위의 예제로부터 만든 Javascript이다.

```tsx
let Greeter = (function () {
  function Greeter(message) {
    this.greeting = message;
  }

  Greeter.prototype.greet = function () {
    return "Hello, " + this.greeting;
  };

  return Greeter;
})();

let greeter;
greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world"
```

`let Greeter`에 constructor function이 할당된다. `new`을 호출하여 이 함수를 실행시키면, 클래스의 인스턴스를 얻는다. constructor function에는 클래스의 모든 static 멤버도 포함된다.

```tsx
class Greeter {
  static standardGreeting = "Hello, there";
  greeting: string;
  greet() {
    if (this.greeting) {
      return "Hello, " + this.greeting;
    } else {
      return Greeter.standardGreeting;
    }
  }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet()); // "Hello, there"

let greeterMaker: typeof Greeter = Greeter; // 클래스 또는 생성자 함수를 가르킴.
greeterMaker.standardGreeting = "Hey there!";

let greeter2: Greeter = new greeterMaker();
console.log(greeter2.greet()); // "Hey there!"

Greeter.standardGreeting = 'Hi, there!';

let greeter3: Greeter;
greeter3 = new Greeter();
console.log(greeter3.greet()); // "Hi, there!"
```

**`typeof Greeter`란 "Greeter 클래스 자체의 타입을 알려달라" 라는 의미이다**. 좀 더 정확하게는 "Greeter라는 symbol의 타입을 알려달라"이다.

## Using a class as an interface

이전 섹션에서 언급했듯이 클래스 선언은 클래스의 인스턴스를 나타내는 타입과 생성자 함수를 만든다. 클래스는 타입을 생성하기 때문에 인터페이스를 사용할 수 있는 동일한 위치에서 사용할 수 있다.

```tsx
class Point {
  x: number;
  y: number;
}

interface Point3d extends Point {
  z: number;
}

let point3d: Point3d = { x: 1, y: 2, z: 3 };
```

## References

- [Typescript Handbook Classes](https://www.typescriptlang.org/docs/handbook/classes.html)
