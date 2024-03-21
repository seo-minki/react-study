---
layout: post
title:  "TypeScript"
author: "최시민"
tags: ["typescript"]
---






# TypeScript Object Type - Class (객체 설계도)




## Class

- TypeScript가 지원하는 class는 **JavaScript ES6의 class와 유사**하지만, 몇 가지 **TypeScript만의 고유한 확장 기능**이 있습니다.
    - TypeScript의 class는 정적 typing과 몇 가지 추가 기능을 제공하여 class를 더욱 강력하고 안전하게 만듭니다.




---




## Class Definition

- JavaScript와 TypeScript의 class 정의 방식은 정적 typing 말고도, **class property(member 변수) 선언 여부**에서도 차이가 있습니다.


### TypeScript에서 Class 정의하기

- JavaScript ES6의 class는 class body에 method만을 포함할 수 있습니다.
- class body에 class property를 선언할 수 없고, 반드시 생성자 내부에서 class property를 선언하고 초기화합니다.

```javascript
// person.js
class Person {
    constructor(name) {
        this.name = name;    // class property의 선언과 초기화
    }

    walk() {
        console.log(`${this.name} is walking.`);
    }
}
```

- JavaScript ES6에서는 문제없이 실행되는 code이지만, file의 확장자를 `ts`로 바꾸어 TypeScript file로 변경한 후 compile하면 compile error가 발생합니다.

```log
person.ts(4,10): error TS2339: Property 'name' does not exist on type 'Person'.
person.ts(8,25): error TS2339: Property 'name' does not exist on type 'Person'.
```


### TypeScript에서 Class 정의하기

- TypeScript class는 class body에 **class property를 사전에 선언**해야 합니다.

```typescript
// person.ts
class Person {
    name: string;    // class property 사전 선언

    constructor(name: string) {
        this.name = name;    // class property에 값 할당
    }

    walk() {
        console.log(`${this.name} is walking.`);
    }
}

const person = new Person('Lee');
person.walk();    // Lee is walking
```




---




## 접근 제한자 (Access Modifier)

- TypeScript class는 class 기반 객체 지향 언어가 지원하는 접근 제한자 `public`, `private`, `protected`를 지원하며, 의미 또한 기본적으로 동일합니다.
    - 접근 제한자는 class 내부의 property과 method의 접근성을 제어하는 keyword입니다.
    - 접근 제한자는 class를 사용하는 외부 code에서 class 내부의 특정 member에 접근할 수 있는지 여부를 결정합니다.

| 접근 가능성 | public | protected | private |
| --- | --- | --- | --- |
| Class 내부 | O | O | O |
| 자식 Class 내부| O | O | x |
| Class Instance | O | x | x |

- `public`으로 지정하고자 하는 member 변수와 method는 접근 제한자를 생략하면 됩니다.
    - TypeScript의 경우, 접근 제한자를 생략한 class property와 method는 암묵적으로 `public`이 선언됩니다.
    - 다른 class 기반 언어의 경우, 접근 제한자를 명시하지 않으면 암묵적으로 `protected`로 지정되어 package level로 공개됩니다.

```typescript
class Foo {
    public x: string;
    protected y: string;
    private z: string;

    constructor(x: string, y: string, z: string) {
        // public, protected, private 접근 제한자 모두 class 내부에서 참조 가능함
        this.x = x;
        this.y = y;
        this.z = z;
    }
}

const foo = new Foo('x', 'y', 'z');

// public 접근 제한자는 class instance를 통해 class 외부에서 참조 가능함
console.log(foo.x);

// protected 접근 제한자는 class instance를 통해 class 외부에서 참조할 수 없음
console.log(foo.y);    // error TS2445: Property 'y' is protected and only accessible within class 'Foo' and its subclasses.

// private 접근 제한자는 class instance를 통해 class 외부에서 참조할 수 없음
console.log(foo.z);    // error TS2341: Property 'z' is private and only accessible within class 'Foo'.

class Bar extends Foo {
    constructor(x: string, y: string, z: string) {
        super(x, y, z);

        // public 접근 제한자는 자식 class 내부에서 참조 가능함
        console.log(this.x);

        // protected 접근 제한자는 자식 class 내부에서 참조 가능함
        console.log(this.y);

        // private 접근 제한자는 자식 class 내부에서 참조할 수 없음
        console.log(this.z);    // error TS2341: Property 'z' is private and only accessible within class 'Foo'.
    }
}
```


### 생성자 Parameter에 접근 제한자 선언

- 생성자(constructor) parameter에도 접근 제한자를 선언할 수 있습니다.
- **접근 제한자가 사용된 생성자 parameter**는 **암묵적으로 class property로 선언**되고, 생성자 내부에서 별도의 초기화가 없어도 **암묵적으로 초기화가 수행**됩니다.


#### 생성자 Parameter에 `private`, `public` 접근 제한자 선언

- `private` 접근 제한자가 사용되면, class 내부에서만 참조 가능하고, `public` 접근 제한자가 사용되면 class 외부에서도 참조가 가능합니다.

```typescript
class Foo {
    // 접근 제한자가 선언된 생성자 parameter 'x'는 class property로 선언되고 지동으로 초기화됨
    constructor(public x: string) { }
}

const foo = new Foo('Hello');
console.log(foo);    // Foo { x: 'Hello' }

// public이 선언된 'foo.x'는 class 외부에서도 참조가 가능함
console.log(foo.x);    // Hello
```

```typescript
class Bar {
    // 접근 제한자가 선언된 생성자 parameter 'x'는 class property로 선언되고 지동으로 초기화됨
    constructor(private x: string) { }
}

const bar = new Bar('Hello');
console.log(bar);    // Bar { x: 'Hello' }

// private이 선언된 'bar.x'는 class 내부에서만 참조 가능함
console.log(bar.x);    // Property 'x' is private and only accessible within class 'Bar'.
```

#### 생성자 Parameter에 접근 제한자를 선언하지 않은 경우

- 생성자 parameter에 접근 제한자를 선언하지 않으면, 생성자 parameter는 생성자 내부에서만 유효한 지역 변수가 되어, 생성자 외부에서 참조가 불가능합니다.

```typescript
class Foo {
    // 'x'는 생성자 내부에서만 유효한 지역 변수임 (접근 제한자가 선언되지 않아 class property 선언과 초기화가 되지 않음)
    constructor(x: string) {
        console.log(x);
    }
}

const foo = new Foo('Hello');
console.log(foo);    // Foo {}
```




---




## 읽기 전용 속성 (Readonly Property)

- TypeScript class의 `readonly` keyword는 변수 할당 시의 `const` keyword와 유사합니다.
- `readonly`가 선언된 class property는 선언 시 또는 생성자 내부에서만 값을 할당할 수 있습니다.
    - 이 외의 경우에는 값을 할당할 수 없고, 오직 읽기만 가능한 상태가 됩니다.
- 일반적으로 상수를 선언할 때 사용합니다.

```typescript
class Foo {
    private readonly MAX_LEN: number = 5;
    private readonly MSG: string;

    constructor() {
        this.MSG = 'hello';
    }

    log() {
        // readonly가 선언된 property는 재할당이 금지됨
        this.MAX_LEN = 10;    // Cannot assign to 'MAX_LEN' because it is a constant or a read-only property.
        this.MSG = 'Hi';    // Cannot assign to 'MSG' because it is a constant or a read-only property.

        console.log(`MAX_LEN : ${this.MAX_LEN}`);    // MAX_LEN : 5
        console.log(`MSG : ${this.MSG}`);    // MSG : hello
    }
}

new Foo().log();
```




---




## Static Member

- TypeScript에서 **`static` keyword를 사용하여 class member를 정적으로 선언**할 수 있습니다.
    - class member에는 method(함수)와 property(속성)가 있으며, 따라서 **static member도 static method와 static property로 나뉩니다.**


### Static Method

- JavaScript ES6의 class에서 `static` keyword는 class의 정적(static) method를 정의합니다.
    - TypeScript에서도 JavaScript ES6와 동일한 방식으로 사용할 수 있습니다.

- 정적 method는 class의 instance가 아닌 **class 이름으로 호출**하기 때문에, class의 instance를 생성하지 않아도 호출할 수 있습니다.
    - 정적 method는 `this`를 사용할 수 없으며, 정적 method 내부의 `this`는 class의 instance가 아닌 class 자신을 가리킵니다.

```javascript
class Foo {
    constructor(prop) {
        this.prop = prop;
    }

    static staticMethod() {
        return 'staticMethod';
    }

    prototypeMethod() {
        return this.prop;
    }
}

// 정적 method는 class 이름으로 호출함
console.log(Foo.staticMethod());    // staticMethod

// 정적 method는 instance로 호출할 수 없음
const foo = new Foo(123);
console.log(foo.staticMethod());    // Uncaught TypeError: foo.staticMethod is not a function.
```


### Static Property

- **TypeScript에서는 static keyword를 class property에도 사용**할 수 있습니다.
- 정적 method와 마찬가지로, 정적 class property는 instance가 아닌 class 이름으로 호출하며, class의 instance를 생성하지 않아도 호출할 수 있습니다.

```typescript
class Foo {
    // 생성된 instance의 갯수
    static instanceCounter = 0;
    constructor() {
        // 생성자가 호출될 때마다 counter를 1씩 증가시킴
        Foo.instanceCounter++;
    }
}

var foo1 = new Foo();
var foo2 = new Foo();

// 정적 class property는 class 이름으로 호출함
console.log(Foo.instanceCounter);    // 2

// 정적 class property는 instance로 호출할 수 없음
console.log(foo2.instanceCounter);    // error TS2339: Property 'instanceCounter' does not exist on type 'Foo'.
```




---




## 추상 Class (Abstract Class)

- 추상 class는 **하나 이상의 추상 method를 포함**하며, 일반 method도 포함할 수 있습니다.
    - 추상 method는 내용이 없이 method 이름과 type만이 선언된 method입니다.

- 추상 class와 추상 method를 선언할 때는 `abstract` keyword를 사용합니다.

- 추상 class는 직접 instance를 생성할 수 없고 상속만을 위해 사용됩니다.
    - 추상 class를 상속한 class는 추상 class의 추상 method를 반드시 구현해야 합니다.

- `interface` type은 추상 class와 비슷하지만, 일반 method를 선언할 수 없다는 점에서 추상 class와 다릅니다.
    - `interface` type은 모든 method가 추상 method입니다.
    - 추상 class는 하나 이상의 추상 method와 일반 method를 포함할 수 있습니다.

```typescript
abstract class Animal {
    // 추상 method
    abstract makeSound(): void;
    // 일반 method
    move(): void {
        console.log('roaming the earth...');
    }
}

// 직접 instance를 생성할 수 없음
new Animal();    // error TS2511: Cannot create an instance of the abstract class 'Animal'.

class Dog extends Animal {
    // 추상 class를 상속한 class는 추상 class의 추상 method를 반드시 구현해야 함
    makeSound() {
        console.log('bowwow~~');
    }
}

const myDog = new Dog();
myDog.makeSound();
myDog.move();
```




---




## Interface 구현 (`implements`)

- class는 `implements` keyword를 사용하여 특정 interface를 구현하겠다고 선언할 수 있습니다.
    - class가 interface의 계약을 준수하도록 강제합니다.

- class가 interface를 구현하는 경우, class는 interface에 정의된 모든 property와 method를 구현해야 합니다.

```typescript
interface Vehicle {
    model: string;
    year: number;
    displayDetails(): void;
}

class Car implements Vehicle {
    model: string;
    year: number;

    constructor(model: string, year: number) {
        this.model = model;
        this.year = year;
    }

    displayDetails() {
        console.log(`Model: ${this.model}, Year: ${this.year}`);
    }
}

const myCar = new Car("Hyundai Sonata", 2020);
myCar.displayDetails();
```

- 하나 이상의 interface를 구현할 수도 있습니다.

```typescript
interface Chargeable {
    batteryLevel: number;
    charge(): void;
}

interface Connectable {
    isConnected: boolean;
    connect(): void;
    disconnect(): void;
}

class Smartphone implements Chargeable, Connectable {
    batteryLevel: number;
    isConnected: boolean;

    constructor(batteryLevel: number) {
        this.batteryLevel = batteryLevel;
        this.isConnected = false;
    }

    charge() {
        this.batteryLevel = 100;
        console.log("Smartphone 충전 완료");
    }

    connect() {
        this.isConnected = true;
        console.log("Smartphone 연결");
    }

    disconnect() {
        this.isConnected = false;
        console.log("Smartphone 연결 해제");
    }
}

const myPhone = new Smartphone(50);
myPhone.charge();
myPhone.connect();
myPhone.disconnect();
```




---




## Reference

- <https://poiemaweb.com/typescript-class>















---
---
---
---
---
---
---
---
---
---














# TypeScript Object Type - Interface (객체 계약서)




## Interface : 객체 계약서

- TypeScript의 interface는 **변수, 함수, class가 특정 구조와 type을 갖추도록 명시**하는 데 사용됩니다.
    - 여러 type의 property로 이루어진 새로운 type을 정의하는 것과 같습니다.
        - interface에 선언된 property 또는 method의 구현을 강제하여 일관성을 유지할 수 있도록 합니다.
    - interface는 **객체가 구현해야 할 구체적인 사항을 지정하고, 지키도록 강제**합니다.

- interface는 compile time에 구조와 type을 검사하기 위해 사용되며, runtime에는 제거됩니다.
    - TypeScript file이 compile된 JavaScript file에는 interface에 대한 code가 없습니다.

- JavaScript ES6는 interface를 지원하지 않지만, TypeScript는 interface를 지원합니다.
    - TypeScript는 interface를 통해 개발자가 더 명확하고 유지보수가 용이한 code를 작성할 수 있도록 합니다.




---




## 변수와 Interface

- interface는 변수의 type으로 사용할 수 있습니다.
- interface를 type으로 선언한 변수는 해당 interface를 준수하여야 합니다.
- interface를 정의하는 것은 새로운 type을 정의하는 것과 유사합니다.

```typescript
interface Todo {
    id: number;
    content: string;
    completed: boolean;
}

// 변수 'todo'의 type으로 'Todo' interface를 선언함
let todo: Todo;

// 변수 todo는 Todo interface를 준수해야 함
todo = { id: 1, content: 'typescript', completed: false };
```

- interface를 사용하여 함수 parameter의 type을 선언할 수 있습니다.
    - 해당 함수에는 함수 parameter의 type으로 지정한 interface를 준수하는 인수를 전달해야 합니다.
    - 함수에 객체를 전달할 때 복잡한 매개 변수 검사 과정이 필요 없어서 매우 유용합니다.

```typescript
interface Todo {
    id: number;
    content: string;
    completed: boolean;
}

let todos: Todo[] = [];

// parameter 'todo'의 type으로 'Todo' interface를 선언함
function addTodo(todo: Todo) {
    todos = [...todos, todo];
}

// parameter 'todo'는 'Todo' interface를 준수해야 함
const newTodo: Todo = { id: 1, content: 'typescript', completed: false };
addTodo(newTodo);
console.log(todos);    // [ { id: 1, content: 'typescript', completed: false } ]
```




---




## 함수와 Interface

- interface는 함수의 type으로 사용할 수 있습니다.
- 함수의 interface에는 type이 선언된 parameter list와 return type을 정의합니다.
- 함수 interface를 구현하는 함수는 interface를 준수하여야 한다.

```typescript
interface SquareFunc {
    (num: number): number;
}

// 함수 interface를 구현하는 함수는 interface를 준수해야 함
const squareFunc: SquareFunc = function (num: number) {
    return num * num;
}

console.log(squareFunc(10));    // 100
```




---




## Class와 Interface

- class 선언문의 `implements` 뒤에 interface를 선언하면, 해당 class는 지정된 interface를 반드시 구현해야 합니다.
    - 이로써 interface를 구현하는 class들은 일관성을 유지할 수 있게 됩니다.

- interface는 property와 method를 가질 수 있다는 점에서 class와 유사하나, 직접 instance를 생성할 수는 없습니다.

```typescript
interface ITodo {
    id: number;
    content: string;
    completed: boolean;
}

// 'Todo' class는 'ITodo' interface를 구현해야 함
class Todo implements ITodo {
    constructor (
        public id: number,
        public content: string,
        public completed: boolean
    ) { }
}

const todo = new Todo(1, 'Typescript', false);
console.log(todo);
```

- interface는 property뿐만 아니라 method도 포함할 수 있으며, 추상 class와 달리 모든 method는 추상 method이어야 한다.
    - 추상 class는 추상 method와 일반 method 모두 가질 수 있습니다.

- interface를 구현하는 class는 interface에서 정의한 property와 추상 methdo를 반드시 구현해야 합니다.

```typescript
interface IPerson {
    name: string;
    sayHello(): void;
}

// 'Person' class는 'IPerson' interface에서 정의한 property와 추상 method를 반드시 구현해야 함
class Person implements IPerson {
    // interface에서 정의한 property 구현
    constructor(public name: string) { }

    // interface에서 정의한 추상 method 구현
    sayHello() {
        console.log(`Hello ${this.name}`);
    }
}

function greeter(person: IPerson): void {
    person.sayHello();
}

const me = new Person('Lee');
greeter(me);    // Hello Lee
```


### 비슷하지만 다른 Interface와 추상 Class

- interface와 추상 class(abstract class)는 method, property의 구조와 type을 강제하고, 추상 method(구현 없이 선언만 한 method)를 가진다는 점에서 비슷하지만, 몇 가지 다른 점이 있습니다.

| Interface | Abstract Class |
| --- | --- |
| 주로 **type check를 위해 사용**됩니다.<br>객체의 구조를 정의하며, 이 구조에 따라 객체가 형성되어야 함을 명시합니다. | **구현과 상속을 위해 사용**됩니다.<br>일부 기능을 구현하고, 나머지 기능은 상속받는 class에서 구현하도록 강제할 수 있습니다. |
| property과 method의 signature(추상 method 등)만을 정의할 수 있으며, **구현을 포함할 수 없습니다.** | 추상 method뿐만 아니라 **구현(일반 method 등)도 포함할 수 있습니다.** |
| class는 **여러 interface를 구현(implement)**할 수 있습니다.<br>이를 통해 **다중 상속**과 유사한 효과를 낼 수 있습니다. | class는 **하나의 추상 class만 상속(extend)**할 수 있습니다.<br>이는 JavaScript와 TypeScript에서 다중 상속을 지원하지 않기 때문입니다. |
| **runtime에는 존재하지 않습니다.**<br>TypeScript를 JavaScript로 compile할 때 interface는 제거됩니다. | **runtime에도 존재합니다.**<br>compile 후에도 JavaScript code에 추상 class는 남습니다. |
| 특별한 keyword 없이 추상 method를 정의합니다. | `abstract` keyword를 사용하여 추상 method를 정의합니다. |

- **다중 상속이 필요하거나 type check만이 목적이라면 interface**를, **구현을 공유하고 싶다면 추상 class**를 사용하는 것이 좋습니다.




---




## 선택적 속성 (Optional Property)

- 선택적 속성(optional property)을 선언하여 **interface의 property를 선택적으로 구현**할 수 있습니다.
    - interface의 일반적인 property는 반드시 구현해야 합니다.

- 선택적 속성은 property 이름 뒤에 물음표(`?`)를 붙이며, 구현을 생략해도 오류가 발생하지 않습니다.

```typescript
interface UserInfo {
    username: string;
    password: string;
    age?: number;
    address?: string;
}

const userInfo: UserInfo = {
    username: 'simin@gmail.com',
    password: '123456'
}

console.log(userInfo);
```




---




## Interface 상속 (Interface 확장)

- interface는 `extends` keyword를 사용하여 interface 또는 class를 상속받을 수 있습니다.

```typescript
interface Person {
    name: string;
    age?: number;
}

interface Student extends Person {
    grade: number;
}

const student: Student = {
    name: 'Lee',
    age: 20,
    grade: 3
}
```

- 복수의 interface를 상속받을 수도 있습니다.

```typescript
interface Person {
    name: string;
    age?: number;
}

interface Developer {
    skills: string[];
}

interface WebDeveloper extends Person, Developer {}

const webDeveloper: WebDeveloper = {
    name: 'Lee',
    age: 20,
    skills: ['HTML', 'CSS', 'JavaScript']
}
```

- interface는 interface뿐만 아니라 class도 상속받을 수 있습니다.
- 이때 class의 모든 member(`public`, `protected`, `private`)가 상속되지만, 구현까지 상속하지는 않습니다.

```typescript
class Person {
    constructor(public name: string, public age: number) {}
}

interface Developer extends Person {
    skills: string[];
}

const developer: Developer = {
    name: 'Lee',
    age: 20,
    skills: ['HTML', 'CSS', 'JavaScript']
}
```




---




## Reference

- <https://poiemaweb.com/typescript-interface>



















---
---
---
---
---
---
---
---
---
---











# TypeScript Object Type - Tuple (고정 배열)




## Tuple Type : 갯수와 Type이 정해진 배열 Type

- TypeScript에서 tuple은 고정된 개수의 요소와 각 요소의 type이 정해진 배열 type입니다.
- tuple을 사용하면 서로 다른 type의 data를 grouping하여 관리할 수 있으며, 각 요소의 정확한 type을 알 수 있어 type 안정성을 보장합니다.
- tuple은 배열과 유사하게 작동하지만, 배열 내의 각 위치에 특정 type을 지정할 수 있다는 점에서 차이가 있습니다.

```typescript
let tuple: [string, number, boolean] = ["", 0, false];
```

- tuple을 정의할 때는 각 요소의 type을 괄호 안에 comma(`,`)로 구분하여 나열합니다.


### 비슷하지만 다른 배열과 Tuple

- 배열(array)과 tuple은 TypeScript에서 data를 순서대로 저장하는 데 사용되는 구조이지만, 각각의 특성과 사용 목적이 다릅니다.
    - 배열은 동일한 type의 data를 다룰 때 사용합니다.
    - tuple은 고정된 수의 서로 다른 type의 data를 다룰 때 사용합니다.

```typescript
/* Array */
let array: number[] = [1, 2, 3, 4, 5];
let genericArray: Array<string> = ["Apple", "Banana", "Cherry"];

/* Tuple */
let tuple1: [string, number] = ["Alice", 30];
let tuple2: [number, string, boolean] = [1, "Steve", true];
```

| 배열 | Tuple |
| --- | --- |
| 동일한 type의 요소만 포함할 수 있음. | 각 요소가 서로 다른 type을 가질 수 있음. |
| 길이가 가변적임. 갯수 제한이 없음. | 길이가 고정적임. 정의된 요소의 개수만큼의 data가 저장됨. |
| type이 동일한 많은 양의 data를 처리할 때 사용. | 여러 type의 data를 한 번에 관리할 필요가 있을 때 사용. |
| 동일한 type의 data 집합을 나타내는 데 사용 | 각 요소의 type을 명시적으로 정의하여 data 구조의 의도를 명확하게 전달하기 위해 사용. |


### Tuple 요소에 접근하기

- tuple 내의 각 요소에 접근하는 것은 배열과 유사하게, index를 통해 이루어집니다.
- 다만, tuple에서는 각 요소가 특정 type으로 지정되어 있기 때문에, 해당 index에 접근하면 TypeScript는 자동으로 올바른 type을 추론합니다.

```typescript
let person: [string, number] = ["Alice", 30];

let personName: string = person[0];    // Alice (string type으로 자동 추론됨)
let personAge: number = person[1];    // 30 (number type으로 자동 추론됨)
```

- `person` tuple은 첫 번째 요소로 문자열(`string`)을, 두 번째 요소로 숫자(`number`)를 가집니다.
- 각 요소에 접근할 때 TypeScript는 해당 index의 type을 알고 있으므로, type 안전성이 보장됩니다.


### Tuple 요소 변경하기

- tuple의 값은 각 요소의 index를 통해 직접 접근하여 변경할 수 있습니다.
- 이때, 변경하려는 요소의 type은 tuple에서 정의된 해당 요소의 type과 일치해야만 합니다.
- 또한 tuple은 고정된 길이를 가지므로, 새로운 요소를 추가하여 tuple의 길이를 변경하는 것은 type 정의에 위배됩니다.

```typescript
let person: [string, number] = ["Alice", 30];

person[0] = "Bob";    // 첫 번째 요소(이름) 변경
person[1] = 32;    // 두 번째 요소(나이) 변경

console.log(person);    // ["Bob", 32]
```

- 각 요소를 변경할 때는 tuple에서 해당 위치에 정의된 type에 맞는 값을 할당해야 합니다.
    - e.g., 숫자 type이 요구되는 위치에 문자열을 할당하려고 하면 TypeScript compiler는 type 오류를 발생시킵니다.

#### Tuple의 요소를 변경할 수 없는 경우

- 읽기 전용 tuple은 변경할 수 없습니다.
- `readonly`로 선언된 tuple은 그 요소를 변경할 수 없으며, 시도하면 compile time에 오류가 발생합니다.

```typescript
let readonlyPerson: readonly [string, number] = ["Alice", 30];

readonlyPerson[0] = "Bob";    // Error: Index signature in type 'readonly [string, number]' only permits reading.
```

- `readonly` tuple은 data의 불변성을 유지해야 할 때 유용하며, 이러한 tuple의 요소는 생성 시에만 할당할 수 있고, 이후에는 변경할 수 없습니다.




---




## Tuple 고급 기능


### 선택적 Tuple 요소

- TypeScript 3.0 이상에서는 tuple 내 요소를 선택적(optional)으로 만들 수 있습니다.
- 선택적 요소는 type 뒤에 물음표 기호(`?`)를 붙여 표시하며, 해당 위치에 값이 있거나 없을 수 있음을 의미합니다.

```typescript
let optionalTuple: [string, number, boolean?] = ["Bob", 25];
```

- `optionalTuple`은 세 번째 요소로 `boolean` type을 가질 수도 있고, 아예 없을 수도 있습니다.


### 나머지 요소와 Tuple

- tuple에서 나머지 요소(rest element)를 사용하여, 특정 위치 이후의 모든 요소에 대해 같은 type을 지정할 수 있습니다.

```typescript
let restTuple: [string, ...number[]] = ["hello", 1, 2, 3];
```

- `restTuple`은 첫 번째 요소로 `string`을, 그리고 나머지 요소로 `number` 배열을 가지는 tuple입니다.


### Tuple과 구조 분해 할당

- tuple은 구조 분해 할당(destructuring assignment)과 함께 사용 가능합니다.
    - 구조 분해를 사용하면 tuple의 각 요소를 개별 변수에 쉽게 할당할 수 있습니다.

```typescript
let employee: [number, string, string] = [1, "Steve", "Developer"];

// 구조 분해 할당을 사용하여 tuple 요소를 변수에 할당
let [id, name, position] = employee;
console.log(id);    // 1
console.log(name);    // Steve
console.log(position);    // Developer
```


### Tuple Type에서의 Spread 연산자

- spread 연산자(`...`)는 tuple type에서도 사용할 수 있습니다.
- spread 연산자로 tuple의 요소를 다른 tuple이나 배열에 쉽게 결합할 수 있습니다.

```typescript
let part1: [number, string] = [1, "Steve"];
let part2: [string, number] = ["Developer", 50000];

let employee: [number, string, string, number] = [...part1, ...part2];
```


### 읽기 전용 Tuple

- TypeScript는 읽기 전용 배열(`ReadonlyArray<T>`)과 유사하게, 읽기 전용 tuple(`readonly [T, U, ...]`)을 지원합니다.
- 읽기 전용 tuple을 사용하면 tuple의 요소를 변경할 수 없게 만들 수 있어, 불변성을 보장할 수 있습니다.

```typescript
let readOnlyTuple: readonly [number, string] = [1, "Steve"];
readOnlyTuple[0] = 2;    // 에러: 읽기 전용 속성이므로 '0'에 할당할 수 없습니다.
```


### Label이 있는 Tuple 요소

- TypeScript 4.0부터는 tuple 요소에 label을 지정할 수 있게 되었습니다.
    - label을 지정하여 코드의 가독성을 높이고, tuple의 구조를 더 명확하게 표현할 수 있습니다.

```typescript
let person: [id: number, name: string] = [1, "Steve"];
```












---
---
---
---
---
---
---
---
---
---












# TypeScript Combined Type (여러 Type이 조합된 Type)




## Combined Type : 두 개 이상의 Type들이 조합된 Type

- TypeScript는 **다양한 type들을 조합**해 복잡한 type checking을 가능하게 하는 고급 type 기능을 제공합니다.
- 이 중 intersection type과 union type은 type의 조합을 표현하는 데 사용되는 두 가지 주요한 방법입니다.
    - 두 조합된 type은 서로 다른 방식으로 type을 조합합니다.


### Intersection Type

- intersection type은 **여러 type을 결합해 모든 type의 특성을 포함하는 새로운 type을 생성**합니다.
- 이는 `&` 연산자를 사용하여 표현되며, 다양한 interface 또는 type을 하나로 합쳐 새로운 type을 정의할 때 유용합니다.

```typescript
interface Employee {
    employeeId: number;
    age: number;
}

interface Manager {
    stockPlan: boolean;
}

type EmployeeManager = Employee & Manager;

const john: EmployeeManager = {
    employeeId: 123,
    age: 30,
    stockPlan: true,
};
```

- `EmployeeManager` type은 `Employee`와 `Manager` interface의 모든 속성을 결합한 intersection type입니다.
- `john` 객체는 이 intersection type에 따라 `Employee`와 `Manager`의 모든 특성을 갖추어야 합니다.


### Union Type

- union type은 **변수가 여러 type 중 하나를 가질 수 있음**을 나타내며, `|` 연산자를 사용하여 type을 정의합니다.
- 이는 함수가 다양한 type의 인자를 받거나, 다양한 type의 값을 반환할 때 유용하게 사용됩니다.

```typescript
type StringOrNumber = string | number;

function logMessage(message: StringOrNumber) {
    if (typeof message === 'string') {
        console.log('String message:', message);
    } else {
        console.log('Number message:', message);
    }
}

logMessage("Hello, TypeScript!");    // String message: Hello, TypeScript!
logMessage(101);    // Number message: 101
```

- `StringOrNumber` union type은 `string` 또는 `number` type의 값을 가질 수 있습니다.
- `logMessage` 함수는 이 union type을 매개 변수로 받아, type에 따라 다른 작업을 수행합니다.




---




## Intersection Type과 Union Type의 차이점

- intersection type은 복잡한 type을 정확하게 표현할 수 있게 하는 반면, union type은 함수나 변수가 더 다양한 type을 유연하게 처리할 수 있도록 합니다.

| Intersection Type | Union Type |
| --- | --- |
| 서로 다른 type들을 결합하여 모든 type의 특성을 포함하는 새로운 type을 정의함 | 여러 type 중 하나의 type을 가질 수 있는 type을 정의함 |
| **'그리고(And)'의 개념**으로, 여러 type의 특성을 **모두 만족**해야 함 | **'또는(Or)'의 개념**으로, 정의된 type 중 **하나만 만족**하면 됨 |





















---
---
---
---
---
---
---
---
---
---












# TypeScript Combined Type - Intersection (객체 속성 합치기)




## Intersection Type - 객체 합치기

- TypeScript의 intersection type은 **여러 type들을 결합해, 모든 type의 속성(property)을 포함하는 복합 type을 생성**합니다.
    - 다양한 interface나 type들의 특성을 통합하여, **필요한 모든 속성을 가진 새로운 type을 정의**할 때 사용됩니다.

- intersection type은 복잡한 data 구조의 표현, 다양한 source의 data 통합, 여러 interface의 구현 등 다양한 상황에 활용될 수 있습니다.
    - 특히 여러 type의 속성을 동시에 만족해야 하는 객체를 다룰 때 유용합니다.

- 여러 type들을 `&` 연산자로 결합하여 intersection type을 만듭니다.

```typescript
type Type1 = {
    a: number;
    b: string;
}

type Type2 = {
    c: boolean;
}

type IntersectionType = Type1 & Type2;

const intersectionType: IntersectionType = {
    a: 1,
    b: "String",
    c: false
};
```


### Intersection Type 사용 주의사항 : Type 충돌

- intersection type을 사용할 때는 주의가 필요합니다.
- **결합되는 type들 사이에 중복되는 속성이 없도록 해야** 하며, 만약 **중복된 속성이 있을 경우 해당 속성은 모든 결합된 type에서 공통으로 만족하는 type이 되어야** 합니다.
    - 서로 다른 type으로 정의된 동일한 이름의 속성을 가진 type들을 결합하려 하면, type 충돌이 발생합니다.

```typescript
interface Product {
    id: number;
    name: string;
}

interface Order {
    id: string;    // Product interface와 동일한 속성명이지만, type이 다름
    quantity: number;
}

type ProductOrder = Product & Order;    // Error : type 충돌
```

- `id` 속성은 `Product` interface에서는 `number` type이고, `Order` interface에서는 `string` type으로 정의되어 있어 type 충돌이 발생합니다.
- 따라서, `ProductOrder` type을 사용하여 객체를 생성하려고 할 때, 오류가 발생합니다.




---




## Intersection Type 활용하기

- type alias 외에도 함수 매개 변수, interface 확장, generic과 같은 여러 방식으로 적용할 수 있어, 상황에 따라 type의 유연성과 재사용성을 높일 수 있습니다.


### Type Alias 합치기

- 두 개의 기본적인 type alias을 정의하고, 이를 합쳐서 intersection type을 만듭니다.

```typescript
// 첫 번째 type 별칭 정의
type Color = {
  color: string;
};

// 두 번째 type 별칭 정의
type Dimension = {
  width: number;
  height: number;
};

// Color와 Dimension type을 결합하여 ColoredRectangle intersection type 생성
type ColoredRectangle = Color & Dimension;

// ColoredRectangle type의 객체 생성 예시
const myRectangle: ColoredRectangle = {
  color: "blue",
  width: 20,
  height: 10
};

console.log(myRectangle);
```

- `ColoredRectangle` intersection type은 `Color` type의 `color` 속성과 `Dimension` type의 `width` 및 `height` 속성을 모두 포함합니다.


### Interface 확장하기

- 서로 다른 interface를 결합하여, 각 interface의 모든 속성을 포함하는 새로운 type을 만듭니다.

```typescript
interface User {
    id: number;
    name: string;
}

interface Permissions {
    permissions: string[];
}

// User와 Permissions의 특성을 모두 갖는 AdminUser type
type AdminUser = User & Permissions;

const admin: AdminUser = {
    id: 1,
    name: "Admin",
    permissions: ["create", "read", "update", "delete"]
};
```

- `User`와 `Permissions` interface를 결합하여 `AdminUser` type을 생성합니다.

#### Interface 확장하기 : 상속 Version

- interface의 상속(`extends`) 기능을 사용하면, intersection type(`&`)을 사용하는 것과 같은 결과를 얻을 수 있습니다.
    - `extends` keyword를 사용하여, 한 interface가 다른 하나 또는 여러 interface의 속성을 상속받도록 할 수 있습니다.

- 여러 interface를 확장하여 새 interface를 만들면, 확장된 interface는 모든 부모 interface의 속성을 포함하게 됩니다.

```typescript
interface User {
    id: number;
    name: string;
}

interface Permissions {
    permissions: string[];
}

// User와 Permissions interface를 상속받아 AdminUser interface 정의
interface AdminUser extends User, Permissions {
}

const admin: AdminUser = {
    id: 1,
    name: "Admin",
    permissions: ["create", "read", "update", "delete"]
};
```

- interface 상속을 사용하여 `User`와 `Permissions` interface의 특성을 모두 갖는 `AdminUser` interface를 정의합니다.
    - `AdminUser` interface는 `User`와 `Permissions` interface의 모든 속성을 상속받습니다.
    - 따라서 `AdminUser` type의 객체를 생성할 때, `User`와 `Permissions` interface에서 정의된 모든 field를 포함해야 합니다.


### 함수 매개 변수의 Type 확장하기

- 함수가 받는 매개 변수의 type을 더 상세하게 지정하고자 할 때, intersection type을 활용할 수 있습니다.

```typescript
interface User {
    id: number;
    name: string;
}

function createUserSession(user: User & { sessionId: string }) {
    console.log(`User ${user.name} has session ID: ${user.sessionId}`);
}

createUserSession({ id: 2, name: "Jane Doe", sessionId: "abc123" });
```


### Generic과 함께 사용하기

- intersection type을 통해 generic을 사용한 기능을 확장하거나 결합할 수 있습니다.

```typescript
function mergeObjects<T, U>(obj1: T, obj2: U): T & U {
    return { ...obj1, ...obj2 };
}

const merged = mergeObjects({ name: "John" }, { age: 30 });
console.log(merged);    // { name: "John", age: 30 }
```




---




## 원시 Type의 Intersection : 가능하지만 쓸모없음

- intersection type을 사용한 원시 type(primitive type)의 결합은 기술적으로 가능하긴 하지만, 유용하지 않습니다.
    - intersection type은 주로 여러 객체 type을 결합하여 새로운 type을 생성하는 데 사용됩니다.
        - 객체 type이 다양한 속성과 method를 갖고 있기 때문에, 여러 type의 속성과 method를 결합하여 더 복합적인 객체 type을 만들 수 있기 때문입니다.

- 원시 type의 intersection을 생성하면, 그 결과는 결합된 모든 type의 특성을 만족하는 type이어야 합니다.
- 서로 다른 원시 type 간에는 공통된 특성이 없기 때문에, 원시 type 간의 결합은 실질적으로 의미가 없습니다.

```typescript
type ImpossibleType = string & number;
```

- `ImpossibleType`은 `string` type과 `number` type의 공통된 특성을 갖는 type이어야 합니다.
- 실제로 `string`과 `number`는 서로 호환되지 않는 type이므로, 이러한 type의 intersection은 어떠한 값도 만족시킬 수 없는 type이 됩니다.











---
---
---
---
---
---
---
---
---
---














# TypeScript Combined Type - Union (여러 Type 허용하기)




## Union Type - 여러 Type 중 하나 선택하기

- union type은 **서로 다른 여러 type 중 하나가 될 수 있는 값을 정의**할 때 사용하는 고급 type입니다. 
    - `|` 연산자를 사용하여 정의되며, 이는 변수나 매개 변수가 지정된 type 중 하나의 type을 가질 수 있음을 의미합니다.

- union type을 사용하면, 변수나 함수 매개 변수가 여러 다른 type 중 하나를 가질 수 있습니다.
    - e.g., `string | number` union type은 해당 변수나 매개 변수가 문자열 또는 숫자일 수 있음을 의미합니다.

- union type은 program이 **다양한 type의 값들을 유연하게 처리할 수 있게** 하며, 다양한 상황에서 유용하게 사용됩니다.
    - e.g., 함수가 여러 type의 인자를 받아들일 수 있도록 하거나, 함수가 여러 type 중 하나의 type을 반환할 수 있도록 할 때 union type을 사용할 수 있습니다.

- **`|` 연산자**로 여러 type들을 연결하여 union type을 정의합니다.

```typescript
function logMessage(message: string | number) {
    console.log(message);
}

logMessage("Hello, TypeScript!");    // 문자열을 인자로 전달
logMessage(100);    // 숫자를 인자로 전달
```


### Union Type과 Type Guard

- union type을 사용하면 변수가 여러 type 중 하나의 type을 가질 수 있음을 의미합니다.
- 이렇게 여러 type 중 하나를 가질 수 있는 변수에 대해서는, 해당 변수가 실제로 어떤 type을 가지는지를 정확히 알아내고, 그에 맞는 method나 속성에 접근하기 위해 type guard가 필요합니다.
- **union type과 type guard를 함께 사용**하면 다양한 type을 가진 변수들을 더 안전하게 처리할 수 있습니다.

#### Union Type과 `typeof` Type Guard

- `typeof` 연산자는 변수의 type을 검사하는 데 사용됩니다.
- `typeof` type guard는 주로 기본 type(`string`, `number`, `boolean`, `undefined`, `object`, `function`)을 검사하는 데 사용됩니다.

```typescript
function combine(input1: string | number, input2: string | number) {
    if (typeof input1 === "string" || typeof input2 === "string") {
        // input1과 input2는 문자열로 취급됨
        return input1.toString() + input2.toString();    // 문자열 결합
    }
    // input1과 input2는 숫자로 취급됨
    return input1 + input2;    // 숫자 합산
}
```

```typescript
const data = { key: 'value' };

if (typeof data === "object" && data !== null) {
    console.log('data는 객체입니다.');
}
```

```typescript
type Combined = object | function;

function doSomething(value: Combined) {
    if (typeof value === "object" && value !== null) {
        // 객체 관련 처리
    } else if (typeof value === "function") {
        // 함수 관련 처리
    }
}
```

#### Union Type과 `instanceof` Type Guard

- `instanceof` 연산자는 객체의 type을 검사(class의 instance인지 여부를 판단)하는 데 사용됩니다.
- `instanceof` type guard는 union type이 class instance를 포함할 때 사용됩니다.

```typescript
class Bird {
    fly() {
        console.log("bird flies");
    }
}

class Fish {
    swim() {
        console.log("fish swims");
    }
}

function move(pet: Bird | Fish) {
    if (pet instanceof Bird) {
        pet.fly();
    } else {
        pet.swim();
    }
}
```

#### Union Type과 사용자 정의 Type Guard

- 사용자 정의 type guard는 더 복잡한 type 검사 logic을 구현할 때 사용됩니다.
    - 사용자 정의 type guard는 `parameter is Type` 형태의 type 예측을 반환하는 함수입니다.

- 사용자 정의 type guard 함수를 통해 특정 type이 확실한 경우에만 해당 type의 속성이나 method에 접근할 수 있도록 합니다.

```typescript
// Fish와 Bird interface 정의
interface Fish {
    swim: () => void;
}
interface Bird {
    fly: () => void;
}

function isFish(pet: Fish | Bird): pet is Fish {
    // Fish type의 객체인지 여부를 판단하는 logic
    return (pet as Fish).swim !== undefined;
}

const pet: Fish | Bird = /* Fish 또는 Bird type의 객체 */;

// 사용자 정의 type guard를 사용하여 type을 좁힘
if (isFish(pet)) {
    pet.swim();    // pet이 Fish로 취급됨
} else {
    pet.fly();    // pet이 Bird로 취급됨
}
```




---




## 다양한 조합의 Union Type

- union type 자체는 특정 "종류"가 있는 것이 아니라, 필요에 따라 어떤 type들을 조합해 사용할 수 있는지에 대한 개념입니다.
    - 즉, union type의 "종류"는 개발자가 정의하는 type들의 조합에 의해 결정됩니다.
- union type을 사용하면 다양한 type의 조합을 생성할 수 있습니다.


### 기본 Type 조합

- 기본적인 type(`string`, `number`, `boolean` 등)을 조합하여 사용할 수 있습니다.

```typescript
let value: string | number;
value = "Hello";
value = 42;    // 유효
```


### Literal Type 조합

- literal type(특정 문자열, 숫자 등의 literal)을 union으로 조합하여, 제한된 값을 갖는 type을 정의할 수 있습니다.

```typescript
type Status = "success" | "failure" | "pending";
```

- literal type을 조합한 union type을 'literal union type'이라고 부릅니다.

#### Literal Union Type

- literal union type은 여러 literal type(주로 문자열 또는 숫자)을 `|` 연산자를 사용해 결합한 것입니다.
- 이를 통해 변수나 parameter가 특정 값들 중 하나를 가져야 함을 명시적으로 선언할 수 있습니다.
    - e.g., 함수가 받을 수 있는 특정 문자열이나 숫자의 집합을 제한하고 싶을 때 유용합니다.

```typescript
type Direction = "up" | "down" | "left" | "right";

function move(direction: Direction) {
    // ...
}

move("up");
move("forward");    // Error: Argument of type '"forward"' is not assignable to parameter of type 'Direction'.
```

- `Direction` type은 `"up"`, `"down"`, `"left"`, `"right"` 중 하나의 값을 가질 수 있습니다.
    - 함수 인자 등에 사용하여, type system을 통해 잘못된 값이 할당되는 것을 compile 시점에 방지할 수 있습니다.


### 객체 Type 조합

- union type은 객체 type에도 적용될 수 있으며, 이를 통해 함수나 component가 다양한 형태의 객체를 처리할 수 있도록 할 수 있습니다.

```typescript
interface Bird {
  fly(): void;
}

interface Fish {
  swim(): void;
}

let pet: Bird | Fish;
```

```typescript
interface Bird {
    type: "bird";
    flyingSpeed: number;
}

interface Horse {
    type: "horse";
    runningSpeed: number;
}

type Animal = Bird | Horse;

function moveAnimal(animal: Animal) {
    let speed;
    switch (animal.type) {
        case "bird":
            speed = animal.flyingSpeed;
            break;
        case "horse":
            speed = animal.runningSpeed;
            break;
    }
    console.log("Moving at speed:", speed);
}

moveAnimal({ type: "bird", flyingSpeed: 10 });    // "Moving at speed: 10"
```

#### 객체 Type 조합 주의사항

- 객체를 union type을 통해서 사용할 때, union type에 속한 모든 type에 공통으로 존재하는 속성에만 접근할 수 있습니다.
- 만약 공통 속성이 없다면, type guard를 사용하여 각 type에 맞는 처리를 해야 합니다.

```typescript
interface Bird {
    fly(): void;
    layEggs(): void;
}

interface Fish {
    swim(): void;
    layEggs(): void;
}

type Pet = Fish | Bird;

function getPet(): Pet {
    // ...
}

let pet = getPet();
pet.layEggs();    // OK: 모든 Pet은 layEggs method를 가지고 있음
pet.swim();    // Error: union type에서는 공통된 속성만 바로 접근할 수 있음
```


### 배열과 Tuple의 조합

- 배열(array)이나 tuple type을 union으로 조합할 수 있습니다.

```typescript
let arr: number[] | string[];
arr = [1, 2, 3];
arr = ["one", "two", "three"];
```


### 함수 Type 조합

- 함수 signature(함수의 매개 변수와 반환 type을 정의한 것)를 union으로 조합할 수 있습니다.

```typescript
let myFunction: ((a: number) => number) | ((b: string) => string);
```


### 고급 Type 조합

- generic type, 조건부(conditional) type 등과 같은 고급(advanced) type과 함께 union type을 사용할 수 있습니다.

```typescript
type Container<T> = T | T[];
```




---




## Union Type 활용하기


### Mapped Type에서의 Union Type

- mapped type을 사용하여 union type을 기반으로 새로운 type을 동적으로 생성할 수 있습니다.
- 이 방법은 union type의 각 member를 변환하거나 수정하여 새로운 type을 생성할 때 유용합니다.

```typescript
type Keys = "firstName" | "lastName";
type PersonRecord = Record<Keys, string>;
// PersonRecord type은 { firstName: string, lastName: string } type과 동일함
```


### 오류 처리에서의 Union Type

- 함수가 여러 type의 error를 반환할 수 있을 때, union type은 각기 다른 error type들을 하나의 type으로 표현하는 데 사용될 수 있습니다.
- 이를 통해 오류 처리 logic을 보다 명확하게 구성할 수 있습니다.

```typescript
type NetworkError = { type: "network"; status: number; message: string };
type TimeoutError = { type: "timeout"; timeout: number; message: string };

// network 요청을 simulation하는 함수
function fetchWithTimeout(url: string): NetworkError | TimeoutError {
    // 예제를 단순화하기 위해, 무작위로 error type을 선택
    if (Math.random() > 0.5) {
        // network error simulation
        return {
            type: "network",
            status: 404,
            message: "Not Found"
        };
    } else {
        // timeout error simulation
        return {
            type: "timeout",
            timeout: 5000,
            message: "Request timed out"
        };
    }
}

// fetchWithTimeout 함수 사용
const result = fetchWithTimeout("https://example.com");

// error type에 따라 다른 처리를 수행
if (result.type === "network") {
    console.log(`Network Error: status code ${result.status}, Message: ${result.message}`);
} else {
    console.log(`TimeOut Error: timeout ${result.timeout}ms, Message: ${result.message}`);
}
```


### 함수 Overloading에서의 Union Type

- 함수 overloading은 함수가 다양한 type의 인자를 받아, 각 type에 따라 다른 동작을 할 수 있도록 하는 기능입니다.
- union type은 함수 overloading을 구현할 때, 인자나 반환 type의 다양성을 제공하는 데 유용합니다.

```typescript
function padLeft(value: string, padding: string | number): string {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    return padding + value;
}
```


### 조건부 Type에서의 Union Type

- 조건부 type(conditional type)은 입력된 type에 따라 다른 type을 반환할 수 있도록 하는 TypeScript 고급 기능입니다.
- union type과 결합하면, 더 동적이고 유연한 type 변환을 정의할 수 있습니다.

```typescript
type Wrapped<T> = T extends infer U[] ? U : T;
type Unwrapped = Wrapped<string[] | number>;    // string | number
```


### React Component와 Props에서의 union type

- React에서 union type은 다양한 종류의 props를 받을 수 있는 component를 정의할 때 유용합니다.
- 이를 통해 단일 component가 다양한 형태의 data를 처리할 수 있도록 할 수 있습니다.

```typescript
interface ImageProps {
    type: "image";
    src: string;
    alt: string;
}

interface TextProps {
    type: "text";
    text: string;
}

type Props = ImageProps | TextProps;

function Content(props: Props) {
    if (props.type === "image") {
        return <img src={props.src} alt={props.alt} />;
    } else {
        return <p>{props.text}</p>;
    }
}
```




---




## Discriminated Union Pattern

- discriminated union(또는 tagged union) pattern은 TypeScript에서 union type을 사용하여 type 안전성을 높이는 design pattern입니다.
    - discriminated union pattern을 사용하면 compile 시점에 각 type을 정확히 구분할 수 있어, type 관련 오류를 방지할 수 있습니다.
    - discriminator(type 구분 값)를 통해 각 type의 목적과 사용 방법이 명확해져, code 가독성이 좋아집니다.
    - 다양한 type을 안전하게 처리하면서도, 각 type에 대한 구체적인 구현을 유연하게 관리할 수 있습니다.

- 이 pattern은 union type 내의 각 type이 공통된 property(discriminator)를 가지고 있으며, 이 discriminator를 사용하여 compile time에 type을 구분할 수 있게 합니다.
    - 서로 다른 여러 type을 하나의 union type으로 결합할 때, 각 type을 구분할 수 있는 공통 속성(discriminator)을 포함시킵니다.
    - 'discriminator'는 각각의 type을 명확하게 식별할 수 있게 하는 literal type의 property이며, 'tag'라고도 불립니다.


```typescript
interface Circle {
    kind: "circle";
    radius: number;
}

interface Square {
    kind: "square";
    sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape): number {
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.sideLength ** 2;
    }
}
```

- discriminated union pattern을 적용하기 위해 3가지 단계가 필요합니다.

1. discriminator(tag) 정의 : union에 속한 각 type을 구별할 수 있는 literal type의 고유한 속성(discriminator)을 정의합니다.
    - `Circle`과 `Square` 두 type은 `kind`라는 discriminator를 포함하고 있으며, 각각 `"circle"`과 `"square"`라는 고유한 값을 가집니다.

2. union type 정의 : discriminator를 포함한 type들을 union으로 결합합니다.
    - `Circle`과 `Square` 두 type을 결합하여 `Shape` union type을 정의합니다.

3. type guard를 이용한 type 구분 : 함수 내에서 type의 discriminator를 검사하여, 각 type에 맞는 적절한 처리를 수행합니다.
    - `getArea` 함수는 `Shape` type의 객체를 인자로 받고, 인자의 `kind` 속성을 검사하여 적절한 type의 면적 계산식을 적용합니다.




















---
---
---
---
---
---
---
---
---
---















# TypeScript Advanced Type (고급 Type)




## 고급 Type : TypeScript의 고급 Type System

- TypeScript의 고급 type system은 code의 재사용성, 유지보수성, 그리고 type 안전성을 향상시키는 다양한 기능을 제공합니다.
- 고급 type에는 generic type, utility type, conditional type, mapped type이 포함됩니다.
- 각각의 type은 TypeScript에서 보다 복잡한 type 관계를 표현하고 다루기 위한 강력한 도구를 제공합니다.


### Generic Type

- generic type은 **type을 parameter처럼 사용**하여, 다양한 type에 대해 작동할 수 있는 component(함수, class, interface 등)를 생성할 수 있게 해줍니다.
- 이를 통해 code의 재사용성을 높이며, type 안전성을 유지할 수 있습니다.

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

- `identity` 함수는 어떤 type의 값이든 받아 동일한 type의 값을 반환합니다.
- generic을 사용함으로써, 이 함수는 다양한 type에 대해 유연하게 사용될 수 있습니다.


### Utility Type

- TypeScript는 **기존 type을 변환하여 새로운 type을 생성**하는 데 도움을 주는 **여러 내장(built-in) utility type**을 제공합니다.
- 이는 code의 중복을 줄이고, type 변환의 편의성을 향상시킵니다.

```typescript
type PartialPoint = Partial<{ x: number; y: number }>;
```

- `Partial` utility type은 모든 property를 선택적으로 만듭니다.
- `PartialPoint` type은 `x`와 `y` property가 모두 선택적인 새로운 type입니다.


### Conditional Type

- conditional(조건부) type은 **type의 조건부 logic을 표현**할 수 있게 해주며, **입력된 type에 따라 다른 type을 반환**할 수 있습니다.

```typescript
type IsNumber<T> = T extends number ? "yes" : "no";
```

- `IsNumber` type은 `T`가 `number` type에 할당 가능한 경우 "yes" type을, 그렇지 않은 경우 "no" type을 반환합니다.


### Mapped Type

- mapped type은 **기존 type의 property를 반복하여 새로운 type을 생성하거나 변형**할 수 있게 해줍니다.

```typescript
type ReadonlyPoint = Readonly<{ x: number; y: number }>;
```

- `Readonly` mapped type은 모든 property를 읽기 전용(readonly)으로 만듭니다.
- `ReadonlyPoint` type은 `x`와 `y`가 읽기 전용인 새로운 type입니다.








---
---
---
---
---
---
---
---
---
---












# TypeScript Advanced Type - Generic Type (Type을 Parameter처럼 사용하기)




## Generic Type : Type을 Parameter처럼 사용하기

- generic type은 **type을 parameter처럼 사용**하여, 다양한 type에 대해 작동할 수 있는 함수, class, interface 등을 생성할 수 있게 해주는 기능입니다.
    - generic을 사용함으로써 type 안전성을 유지하면서, code를 재사용 가능하고 유연하게 만들 수 있스빈다.

- **generic을 사용하면, 하나의 함수나 class가 여러 type에 대해 작동할 수 있게 됩니다.**
    - code를 작성할 때 구체적인 type을 명시하는 대신, **type 변수(T)를 사용하여 함수나 class를 정의**합니다.
    - type 변수는 함수나 class가 호출되거나 instance를 생성할 때 구체적인 type으로 대체됩니다.


### 함수의 Generic

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let output1 = identity<string>("myString");    // output1의 type은 'string'
let output2 = identity<number>(100);    // output2의 type은 'number'
```

- `identity` 함수는 어떤 type의 인자도 받을 수 있고, 받은 인자와 동일한 type으로 값을 반환합니다.
    - `T`는 type 변수로, 함수가 호출될 때 결정됩니다.

- `identity` 함수 호출 시, `<string>`, `<number>`와 같이 type 인자를 제공하여 `T`의 구체적인 type을 지정합니다.
    - 이를 통해 compiler는 반환 값의 type을 정확히 알 수 있습니다.


### Interface의 Generic

- generic은 interface에도 사용될 수 있으며, 이를 통해 다양한 type을 가질 수 있는 객체를 정의할 수 있습니다.

```typescript
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

- `GenericIdentityFn` interface는 generic을 사용하여 정의되었습니다.
- `myIdentity` 함수는 이 interface의 구현체로, `number` type의 인자를 받고 `number` type을 반환하는 함수로 지정됩니다.



### Class의 Generic

- generic은 class 정의에도 사용될 수 있으며, 이를 통해 다양한 type을 가질 수 있는 class instance를 생성할 수 있습니다.

```typescript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

- `GenericNumber` class는 type `T`에 대해 generic입니다.
- `myGenericNumber` instance는 `number` type을 사용하여 생성되며, 이로 인해 해당 instance의 `zeroValue` property와 `add` method는 모두 `number` type을 사용하게 됩니다.




---




## Generic Constraint : 제약 조건 (`extends`)

- generic type을 사용할 때, 특정 property나 method에 접근하고 싶을 수 있습니다.
- 이럴 때 generic type에 제약 조건(constraint)을 사용하여, **특정 type이 가져야 할 구조를 명시**할 수 있습니다.
- generic type에 제약 조건을 추가하면, **generic type이 특정 속성이나 method를 가지고 있음을 강제**할 수 있습니다.

- 제약 조건을 통해, 함수나 class 내부에서 generic type에 대해 보다 안전하게 연산을 수행할 수 있습니다.
    - e.g., generic type이 특정 interface를 구현하도록 강제할 수 있습니다.

```typescript
<T extends Type>
```

- 제약 조건은 `extends` keyword를 사용하여 정의합니다.

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);    // `.length` property를 안전하게 사용할 수 있음
    return arg;
}
```


### Intersection Type으로 제약 조건 추가하기

- intersection type(`&`)과 함께 generic을 사용하여, 여러 type의 특성을 모두 가진 새로운 type을 생성할 수 있습니다.
- 이 방식은 generic type이 여러 interface를 구현하거나, 여러 type의 특성을 조합해야 할 때 유용합니다.

```typescript
interface Identifiable {
    id: number;
}

interface Nameable {
    name: string;
}

function createItem<T extends Identifiable & Nameable>(item: T): T {
    console.log(`Item ${item.name} has ID: ${item.id}`);
    return item;
}
```




---




## Generic Default Type Parameter : 기본 값

- **generic type 기본 값(generic default type parameter)**은 TypeScript에서 **generic을 사용할 때 제공되지 않은 type 인자에 대해, 기본 type을 설정할 수 있게 해주는 기능**입니다.
    - generic type 기본 값을 통해 개발자는 generic 함수나 class를 더 유연하게 사용할 수 있으며, **type 인자를 생략했을 때의 동작을 명시적으로 정의**할 수 있습니다.

- generic type 기본 값을 사용하면 TypeScript의 type 추론(type inference) 기능이 동작합니다.
    - type 인자를 명시적으로 제공하지 않아도, TypeScript compiler는 제공된 값의 type을 기반으로 적절한 type을 추론하거나, 기본 값을 사용하여 type을 결정합니다.

- generic type 기본 값은 code를 더욱 간결하고 유연하게 만듭니다.
    - type 인자가 생략됐을 때 사용될 type을 명시적으로 지정함으로써, code의 가독성과 이해도를 높일 수 있습니다.
    - 함수나 class를 호출할 때마다 다른 type을 지정할 수 있는 option을 제공하면서도, 특정 상황에서는 기본 type을 자동으로 사용하게 함으로써 code의 유연성을 향상시킵니다.
    - 기본 값을 통해 안전하게 type을 지정함으로써, 잘못된 type 사용으로 인한 오류를 줄일 수 있습니다.

- generic type 기본 값은 `T = DefaultType` 형식을 사용하여 정의됩니다.
    - `T`는 generic type 매개 변수이며, `DefaultType`은 해당 매개 변수가 생략됐을 때 사용될 기본 type입니다.

```typescript
function createContainer<T = string>(value: T): {value: T} {
    return {value};
}

let container = createContainer('Hello');
```

- `createContainer` 함수는 generic type 매개 변수 `T`에 대한 기본 값으로 `string`을 사용합니다.
- 이는 함수를 호출할 때 type 인자를 명시하지 않아도, 자동으로 `T`가 `string`으로 설정됨을 의미합니다.
- 따라서, `container` 변수는 `{value: string}` type의 객체를 가지게 됩니다.


### 기본 값이 있는 Generic 함수

- 배열을 받아 그 배열의 첫 번째 요소를 반환하는, generic type 기본 값을 사용한 함수입니다.
- type 인자가 제공되지 않은 경우, 기본적으로 `number[]` type의 배열을 처리하도록 합니다.

```typescript
function getFirstElement<T = number>(arr: T[]): T | undefined {
    return arr[0];
}

const firstNumber = getFirstElement([10, 20, 30]);    // type 인자를 생략 (기본 값 number 사용)
const firstString = getFirstElement<string>(['apple', 'banana', 'cherry']);    // type 인자로 string 명시

console.log(firstNumber);    // 출력: 10
console.log(firstString);    // 출력: apple
```

- `getFirstElement` 함수는 generic type `T`에 대한 기본 값으로 `number`를 가집니다.
- 따라서 type 인자를 생략하고 숫자 배열을 인자로 전달하면, 함수는 숫자를 반환합니다.
- 반면, type 인자로 `string`을 명시하면, 문자열 배열을 처리합니다.


### 기본 값이 있는 Generic class

- generic type 기본 값을 사용하는, data wrapper class입니다.
    - data wrapper class는 data를 저장하고, 저장된 data를 반환하는 기능을 가집니다.

- type 인자가 제공되지 않은 경우, 기본적으로 `string` type을 다룹니다.

```typescript
class DataWrapper<T = string> {
    private data: T;

    constructor(data: T) {
        this.data = data;
    }

    getData(): T {
        return this.data;
    }
}

const stringWrapper = new DataWrapper('Hello, World!');    // type 인자를 생략 (기본 값 string 사용)
const numberWrapper = new DataWrapper<number>(12345);    // type 인자로 number 명시

console.log(stringWrapper.getData());    // 출력: Hello, World!
console.log(numberWrapper.getData());    // 출력: 12345
```

- `DataWrapper` class는 generic type `T`에 대한 기본 값으로 `string`을 가집니다.
- 생성자를 통해 data를 instance에 저장하고, `getData` method를 사용하여 저장된 data를 반환합니다.
- type 인자를 생략하면 문자열을 다루는 것으로 간주되며, 명시적으로 다른 type을 지정하면 해당 type의 data를 다룹니다.





















---
---
---
---
---
---
---
---
---
---






# TypeScript Advanced Type - Utility Type (Type을 조작하기 위한 내장 Type)




## Utility Type

- TypeScript의 utility type은 type system 내에서 **type을 효과적으로 재사용하고 조작하기 위해 설계된 built-in(내장) type 집합**입니다.
    - 개발자가 보다 선언적이고 간결한 방식으로 type을 정의하고, type 조작을 통해 새로운 type을 생성할 수 있게 해줍니다.

- utility type은 일반적인 type 변환을 쉽게 수행할 수 있도록, TypeScript에서 기본으로 제공하는 미리 정의된 type 집합을 제공합니다.
    - utility type들을 사용하면, 기존 type을 변형하여 새로운 type을 생성하는 등의 작업을 간편하게 할 수 있습니다.
        - TypeScript type system의 강력한 type 변환 기능을 활용하여, 복잡한 type 조작을 보다 간편하고 안전하게 수행할 수 있습니다.

- utility type에는 `Partial`, `Readonly`, `Record`, `Pick`, `Omit` 등, 특정한 type 변환 작업을 위해 설계된 다양한 type이 있습니다.

| Utility Type | 설명 |
| --- | --- |
| `Partial<T>` | type `T`의 모든 속성을 선택적으로 만듦. |
| `Readonly<T>` | type `T`의 모든 속성을 읽기 전용으로 만듦. |
| `Record<K,T>` | key의 type이 `K`이고 값의 type이 `T`인 객체 type을 생성함. |
| `Pick<T,K>` | type `T`에서 속성 `K`만을 선택하여 구성된 type을 생성함. |
| `Omit<T,K>` | type `T`에서 속성 `K`를 제외한 type을 생성함. |
| `Exclude<T,U>` | type `T`에서 `U`에 할당할 수 있는 모든 속성을 제외함. |
| `Extract<T,U>` | type `T`에서 `U`에 할당할 수 있는 모든 속성만을 추출함. |
| `NonNullable<T>` | type `T`에서 `null`과 `undefined`를 제외한 type을 생성함. |
| `Parameters<T>` | 함수 type `T`의 매개 변수 type들을 tuple type으로 생성함. |
| `ConstructorParameters<T>` | class 생성자 type `T`의 매개 변수 type들을 tuple type으로 생성함. |
| `ReturnType<T>` | 함수 type `T`의 반환 type을 생성함. |
| `InstanceType<T>` | 생성자 함수 type `T`의 instance type을 생성함. |
| `Required<T>` | type `T`의 모든 속성을 필수로 만듦. |
| `ThisParameterType` | 함수 type의 `this` 매개 변수의 type을 추출함. TypeScript 3.2에서 추가됨. |
| `OmitThisParameter` | 함수 type에서 `this` 매개 변수 type을 제거함. TypeScript 3.0에서 추가됨. |
| `ThisType<T>` | 객체 literal이나 interface에서 `this`의 type을 지정함. TypeScript 2.0에서 추가됨. `--noImplicitThis` flag 필요. |


### Utility Type을 사용하는 이유

1. **type 안전성 향상** : utility type을 사용함으로써 개발자는 기존 type의 구조를 유지하면서, 특정 속성을 수정하거나 추가하는 등의 type 조작을 안전하게 수행할 수 있습니다.
    - code 전반에 걸쳐 일관된 type 안전성을 보장합니다.

2. **code 재사용성 증대** : utility type을 활용하면, 기존 type을 기반으로 새로운 type을 쉽게 생성하고 조작할 수 있습니다.
    - 비슷한 type pattern이 반복될 때 code 중복을 줄이고, 재사용성을 높이는 데 도움이 됩니다.

3. **개발 생산성 향상** : utility type을 사용하면 복잡한 type 조작을 간단한 선언으로 해결할 수 있어, 개발 과정이 간소화됩니다.
    - 개발자가 보다 신속하게 type-safe한 code를 작성할 수 있게 해주며, 결과적으로 생산성을 증대시킵니다.

4. **유연성과 확장성 제공** : utility type들은 다양한 type 조작을 위한 기능을 제공하여, 변경에 유연하게 대처할 수 있게 합니다.
    - project의 요구 사항이 변경되어 type을 수정하거나 확장해야 할 때 유연하게 대응할 수 있도록 해줍니다.




---




## Utility Type의 종류


### `Partial<T>`

- type `T`의 모든 속성을 선택적으로 만듭니다.

```typescript
interface Todo {
    title: string;
    description: string;
}

const updateTodo: Partial<Todo> = { title: "new title" };
```


### `Readonly<T>`

- type `T`의 모든 속성을 읽기 전용(readonly)으로 만듭니다.

```typescript
interface Todo {
    title: string;
}

const myTodo: Readonly<Todo> = { title: "Readonly title" };
myTodo.title = "new title";        // 오류: 'title'은 읽기 전용 속성이므로 할당할 수 없습니다.
```


### `Record<K, T>`

- key의 type이 `K`이고 값의 type이 `T`인 객체 type을 생성합니다.

```typescript
type Page = "home" | "about" | "contact";
const pageinfo: Record<Page, string> = {
    home: "Welcome",
    about: "About us",
    contact: "Contact us",
};
```


### `Pick<T, K>`

- type `T`에서 속성 `K`만을 선택하여 구성된 type을 생성합니다.

```typescript
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;
```


### `Omit<T, K>`

- type `T`에서 속성 `K`를 제외한 type을 생성합니다.

```typescript
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Omit<Todo, "description">;
```


### `Exclude<T, U>`

- type `T`에서 `U`에 할당할 수 있는 모든 속성을 제외합니다.

```typescript
type T = string | number | boolean;
type TWithoutBoolean = Exclude<T, boolean>;
```


### `Extract<T, U>`

- type `T`에서 `U`에 할당할 수 있는 모든 속성만을 추출합니다.

```typescript
type T = string | number | boolean;
type TOnlyNumber = Extract<T, number>;
```


### `NonNullable<T>`

- type `T`에서 `null`과 `undefined`를 제외한 type을 생성합니다.

```typescript
type T = string | number | null | undefined;
type NonNullableT = NonNullable<T>;
```


### `Parameters<T>`

- 함수 type `T`의 매개 변수 type들을 tuple type으로 생성합니다.

```typescript
function doSomething(name: string, age: number): void {}

type Params = Parameters<typeof doSomething>;
```


### `ConstructorParameters<T>`

- class 생성자 type `T`의 매개 변수 type들을 tuple type으로 생성합니다.

```typescript
class Todo {
    constructor(public title: string, public description: string) {}
}

type CtorParams = ConstructorParameters<typeof Todo>;
```


### `ReturnType<T>`

- 함수 type `T`의 반환 type을 생성합니다.

```typescript
function getAge(): number {
    return 30;
}

type Age = ReturnType<typeof getAge>;
```


### `InstanceType<T>`

- 생성자 함수 type `T`의 instance type을 생성합니다.

```typescript
class Todo {
    title: string;
    constructor(title: string) { this.title = title; }
}

type TodoInstance = InstanceType<typeof Todo>;
```


### `Required<T>`

- type `T`의 모든 속성을 필수로 만듭니다.

```typescript
interface Props {
    a?: number;
    b?: string;
}

const props: Required<Props> = { a: 5, b: "Required" };
```


### `ThisParameterType`

- 함수 type의 `this` 매개 변수의 type을 추출합니다.

```typescript
function toHex(this: Number) {
    return this.toString

(16);
}

type HexThis = ThisParameterType<typeof toHex>;
```


### `OmitThisParameter`

- 함수 type에서 `this` 매개 변수 type을 제거합니다.

```typescript
function toHex(this: Number) {
    return this.toString(16);
}

const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);
```


### `ThisType<T>`

- `ThisType<T>` type은 객체 literal이나 interface에서 `this`의 type을 지정합니다.
- 주로 객체 literal의 메소드에서 `this`의 type을 명시적으로 선언할 때 사용됩니다.
- `ThisType<T>`는 TypeScript의 `--noImplicitThis` flag가 활성화된 경우에 유용합니다.

```typescript
type ObjectWithThis = { hello: string } & ThisType<{ world: string }>;

const obj: ObjectWithThis = {
    hello: "Hello",
    getWorld() {
        return this.world;    // `this`의 type이 { world: string }으로 명시적으로 지정됨
    }
};
```

- `ThisType<T>`는 직접적인 예제로 설명하기 어렵습니다.
- `ThisType<T>` type은 주로 mixin이나 고급 객체 구성 pattern에서 사용됩니다.

















---
---
---
---
---
---
---
---
---
---












# TypeScript Advanced Type - Conditional Type (조건에 따라 Type 결정하기)


## 조건부 Type : 특정 조건에 따라 Type 결정하기

- 조건부(conditional) type은 TypeScript에서 **특정 조건에 따라 type을 결정**할 수 있게 해주는 고급 type system의 기능입니다.
    - TypeScript 2.8 version에서 도입되었습니다.

- 조건부 type을 통해 **type의 형태를 동적으로 조작**할 수 있어, 복잡한 type 관계를 표현할 때 유용합니다.
    - type을 programming 언어의 `if` 문처럼 다룰 수 있게 해주어, type의 선택적 사용을 가능하게 합니다.

- 많은 utility type들이 조건부 type을 사용하여 구현되어 있습니다.
    - e.g., `Partial<T>`, `Required<T>`, `Readonly<T>`, `Record<K, T>`, `Pick<T, K>`, `Exclude<T, U>`, `Extract<T, U>`, `NonNullable<T>`, `ReturnType<T>`, `InstanceType<T>`.
    - 따라서 조건부 type으로 기능을 구현하기 전에, TypeScript에서 미리 만들어 놓은 utility type이 있는지 확인하는 것이 좋습니다.

- 조건부 type은 JavaScript에 있는 삼항 연산자 조건문(`condition ? trueExpression : falseExpression`) 같은 형태를 가집니다.

```typescript
SomeType extends OtherType ? TrueType : FalseType;
```

- `extends`를 기준으로 왼쪽에 있는 type이 오른쪽 type에 할당할 수 있다면 첫 번째 분기('참' 값 분기)를, 그렇지 않다면 뒤의 분기('거짓' 값 분기)를 얻게 됩니다.

```typescript
interface Animal {
    live(): void;
}
interface Dog extends Animal {
    woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string;    // type Example1 = number;
type Example2 = RegExp extends Animal ? number : string;    // type Example2 = string;
```

- `Dog extends Animal`에 따라 type이 `number`인지 `string`인지 결정됩니다.




---




## Generic Type과 함께 사용하기

- 조건부 type은 일반적으로 generic type과 함께 사용됩니다.
    - 조건부 type만 사용하는 것보다 더 유용하기 때문입니다.

```typescript
T extends U ? X : Y;
```

- `T`와 `U`는 generic type입니다.

- 조건부 type을 generic type과 함께 사용할 때는 `T extends U ? X : Y` 형태의 구문을 사용하여, **type `T`가 `U`를 확장(상속)한다면 `X` type을, 그렇지 않다면 `Y` type을 결과로 반환**합니다.
    - `T`가 `U`에 할당 가능한 경우에는 결과 type이 `X`가 되고, 그렇지 않은 경우에는 `Y`가 됩니다.
        - "`T`가 `U`에 할당 가능한 경우"는 "`T`가 `U`의 하위 type인 경우"를 의미합니다.


### 조건부 Type 적용 전

- `createLabel` 함수는 입력 값의 type에 따라 반환 값의 type이 달라지기 때문에, 여러 개의 함수를 만들어 overloading합니다.

```typescript
interface IdLabel {
    id: number;
}
interface NameLabel {
    name: string;
}
 
function createLabel(id: number): IdLabel;
function createLabel(name: string): NameLabel;
function createLabel(idOrName: number | string): IdLabel | NameLabel;
function createLabel(idOrName: number | string): IdLabel | NameLabel {
    throw "unimplemented";
}
```

- 함수를 직접 선언하여 overloading하는 방식을 사용하면 program 전체에서 매번 비슷한 종류의 함수를 만들어야 합니다.
    - `createLabel` 함수에서 새로운 type을 다루고 싶을 때마다 overloading 함수의 수는 계속해서 늘어납니다.
    - 이는 효율적이지 못하며, overloading 함수를 계속 만드는 것은 번거로운 작업입니다.

- **generic type과 조건부 type을 함께 사용하여**, 함수를 더 간편하게 overloading할 수 있습니다.


### 조건부 Type 적용 후

- 여러 type에 대한 case를 위해 함수를 overloading하는 것 대신, **generic type에 조건부 type을 적용하여 overloading된 함수 수를 줄일 수 있습니다.**

```typescript
interface IdLabel {
    id: number;
}
interface NameLabel {
    name: string;
}
 
type IdOrName<T extends number | string> = T extends number ? IdLabel : NameLabel;
function createLabel<T extends number | string>(idOrName: T): IdOrName<T> {
    throw "unimplemented";
}

let a = createLabel("typescript");    // let a: NameLabel
let b = createLabel(2.8);    // let b: IdLabel
let c = createLabel(Math.random() ? "hello" : 42);    // let c: NameLabel | IdLabel
```

- 조건부 type을 사용하면 단일 함수까지 overloading 없이 단순화시킬 수 있습니다.


### 재귀 참조 불가

- union type, intersection type과 유사하게, **조건부 type은 재귀적으로 자기 자신을 참조할 수 없습니다.**

```typescript
type ElementType<T> = T extends any[] ? ElementType<T[number]> : T;    // Error
```




---




## 분산적인 조건부 Type (Distributive Conditional Type)

- 조건부 type이 generic type과 함께 사용될 때, **type 인수(argument)로 union type을 받으면 분산적으로 동작**하게 됩니다.
    - 조건부 type에 대한 instance를 생성하는 과정에서 자동으로 union type으로 분산됩니다.
    - union type의 각 요소(member)에 대해 개별적으로 조건을 평가하여 결과 type을 결정합니다.

- `T`에 대한 type 인수로 `A | B | C`를 사용하여 `T extends U ? X : Y` type을 instance로 만들면, `(A extends U ? X : Y) | (B extends U ? X : Y) | (C extends U ? X : Y)` 변환되어 type이 결정됩니다.

```typescript
type ToArray<T> = T extends any ? T[] : never;

type StrArrOrNumArr = ToArray<string | number>;    // type StrArrOrNumArr = string[] | number[]
```

1. `ToArray` 조건부 type을 적용시키기 위해, type 인수로 받은 union type(`string | number`)을 가져옵니다.
2. `ToArray` 조건부 type은 **union type의 member들을 분리**하여, 각 member에 배열로 변환하는 작업을 따로 적용합니다.
    - 각 member에 따로 적용하기 때문에, 변환 작업은 **`ToArray<string> | ToArray<number>`로 진행**합니다.
        - `ToArray<string | number>`이 아닙니다.
3. `StrArrOrNumArr` type은 분산성이 적용된 결과인 **`string[] | number[]` type을 최종적으로 반환**합니다.


### 분산 조건부 Type 사용하지 않기 : 분산 동작 방지

- 분산성이 적용된 일반적인 동작을 원하지 않으면, **`extends` keyword의 양 옆 요소들을 대괄호(`[]`)로 감싸서** 분산을 방지할 수 있습니다.

```typescript
type ToArrayNonDist<T> = [T] extends [any] ? T[] : never;

type StrArrOrNumArr = ToArrayNonDist<string | number>;    // type StrArrOrNumArr = (string | number)[]
```

- `ToArrayNonDist` 조건부 type은 분산 동작을 하지 않도록 요소들을 대괄호로 감쌌기 때문에, **type 인수로 union type이 들어와도 분산적인 동작을 하지 않습니다.**
    - union type의 member들을 분리하지 않고, **union type 자체에 배열 변환 작업**을 합니다.
    - 따라서 변환 작업은 **`ToArray<string | number>`로 진행**하고, **결과도 `(string | number)[]`로 반환**합니다.


### 분산 조건부 Type 활용 예제 : Union Type Filtering

```typescript
type Diff<T, U> = T extends U ? never : T;    // U에 할당할 수 있는 type을 T에서 제거
type Filter<T, U> = T extends U ? T : never;    // U에 할당할 수 없는 type을 T에서 제거

type T1 = Diff<"a" | "b" | "c" | "d", "a" | "c" | "f">;    // "b" | "d"
type T2 = Filter<"a" | "b" | "c" | "d", "a" | "c" | "f">;    // "a" | "c"
type T3 = Diff<string | number | (() => void), Function>;    // string | number
type T4 = Filter<string | number | (() => void), Function>;    // () => void

type NonNullable<T> = Diff<T, null | undefined>;    // T에서 null과 undefined를 제거

type T5 = NonNullable<string | number | undefined>;    // string | number
type T6 = NonNullable<string | string[] | null | undefined>;    // string | string[]

function f1<T>(x: T, y: NonNullable<T>) {
    x = y;    // 성공
    y = x;    // 오류
}

function f2<T extends string | undefined>(x: T, y: NonNullable<T>) {
    x = y;    // 성공
    y = x;    // 오류
    let s1: string = x;    // 오류
    let s2: string = y;    // 성공
}
```




---




## 조건부 Type의 Type 추론 : `infer` Keyword

- `infer` keyword는 TypeScript의 고급 type 기능 중 하나로, **조건부 type(`T extends U ? X : Y`) 내에서 사용되어 type을 동적으로 추론**하는 데 사용됩니다.
    - `infer` keyword를 통해 조건부 type의 type 추론(type inference) 기능을 사용할 수 있으며, **'참' 값 분기에서 비교하는 type을 추론**합니다.

- `infer` keyword는 주로 generic type과 함께 사용되며, 특정 type의 구조에서 일부를 동적으로 추론할 때 유용합니다.
    - e.g., 함수 type의 매개 변수나 반환 type을 추론하거나, generic type에서 특정 type을 추출하는 데 사용됩니다.


```typescript
T extends (infer U) ? X : Y
```

- 조건부 type을 사용할 때, `T extends U ? X : Y` 형태의 조건문에서 `U`의 위치에 `infer` keyword를 사용하여 type을 추론할 수 있습니다.
    - `infer` keyword 다음에 나오는 변수(e.g., `infer R`)는 조건부 type이 참인 경우에만 type을 추론하며, 이 변수는 추론된 type을 나타내는 데 사용됩니다.

- `infer` keyword의 사용은 복잡한 type 조작을 단순화하고, code의 가독성을 높이며, 재사용 가능한 type을 생성하는 데 큰 도움을 줍니다.
    1. **복잡한 type 추론 가능** : `infer`를 사용하면, 복잡한 구조에서 특정 type을 추출하거나, 함수의 반환 type, tuple의 요소 type 등을 쉽게 추론할 수 있습니다.
    2. **가독성 향상** : 복잡한 type 연산을 명확하고 간결한 방식으로 표현할 수 있어, type 정의를 이해하기 쉬워집니다.
    3. **재사용성 증가** : 추론된 type을 다양한 곳에서 재사용할 수 있어, type code의 중복을 줄이고 유지보수성을 향상시킵니다.

- `infer` keyword는 조건부 type의 **`true` 분기에서만 사용할 수 있습니다.**
- 추론할 type이 명확하지 않거나 조건부 type이 항상 참이 아닌 경우, **`never` type을 반환하도록 설계하는 것이 일반적**입니다.


### 배열 요소의 Type 추론

```typescript
type ElementType<T> = T extends (infer U)[] ? U : never;

type Item = ElementType<number[]>;    // number
type NoItem = ElementType<string>;    // never
```

- `ElementType<T>` type은 배열 `T`의 요소 type을 추론합니다.
    - `T`가 배열이라면, `infer U`를 사용하여 배열의 요소 type을 `U`로 추론하고, 그 type을 반환합니다.
    - 배열이 아닌 경우에는 `never` type을 반환합니다.


### 함수 반환 Type 추론

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Func = () => number;
type FuncReturnType = ReturnType<Func>;    // number
```

- `ReturnType<T>` 조건부 type을 사용하여, generic type `T`가 함수 type일 경우 그 함수의 반환 type을 추론합니다.
    - `T`가 함수 type이라면, 해당 함수의 반환 type을 `infer R`를 사용하여 `R`로 추론하고, 그 type을 반환합니다.
    - 함수 type이 아닌 경우에는 `never`를 반환합니다.

- `T extends (...args: any[]) => infer R ? R : never` 구문에서 `infer R` 부분은 `T` type의 함수가 반환하는 type을 `R`로 추론하라는 의미입니다.


### Promise의 결과 Type 추론

```typescript
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type PromiseType = Promise<number>;
type ResolvedType = UnwrapPromise<PromiseType>;    // number
type NonPromiseType = UnwrapPromise<string>;    // string
```

- `UnwrapPromise<T>` type은 `T`가 `Promise`의 instance일 경우, promise가 해결(`resolve`)될 때의 type을 추론합니다.
    - 만약 `T`가 `Promise` type이라면, 그 안에 감싸진 type을 `infer U`를 사용하여 추론하고 반환합니다.
    - `T`가 promise가 아니라면, `T` 자체를 반환합니다.


### 객체에서 특정 Property의 Type 추론

```typescript
type PropertyType<T, K extends keyof T> = T extends { [P in K]: infer U } ? U : never;

interface Example {
    name: string;
    age: number;
}

type NameType = PropertyType<Example, 'name'>;    // string
type AgeType = PropertyType<Example, 'age'>;    // number
```

- `PropertyType<T, K>` type은 객체 type `T`에서 key `K`에 해당하는 property의 type을 추론합니다.
    - `PropertyType<T, K>` type은 객체 `T`가 key `K`를 가지고 있고, 해당 key의 type을 `infer U`를 통해 `U`로 추론할 수 있다면, 그 type을 반환합니다.
    - 만약 `T`에 `K` key가 없다면, `never` type을 반환합니다.




---




## 다양한 조건부 Type 사용 예시


### 조건부 Type을 사용한 Type Filtering

```typescript
type IsString<T> = T extends string ? 'yes' : 'no';

type A = IsString<string>;    // 'yes'
type B = IsString<number>;    // 'no'
```

- `IsString` type은 generic type `T`가 `string`에 할당 가능한지 검사하여, 그 결과에 따라 `'yes'` 또는 `'no'`라는 literal type을 반환합니다.

#### `never` Type Filtering

- 조건부 type을 사용하여 특정 조건을 만족하는 type만을 추출할 수 있습니다.
- `never` type은 TypeScript에서 "type 없음"을 나타내므로, 조건에 맞지 않는 type을 filtering할 때 유용하게 사용됩니다.

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
```

- `NonNullable` type은 `null`이나 `undefined`를 제외한 type을 생성합니다.


### 조건부 Type을 사용한 Type 추출

```typescript
type ExtractStringOrNumber<T> = T extends string | number ? T : never;

type C = ExtractStringOrNumber<string | boolean | number>;    // string | number
```

- `ExtractStringOrNumber` type은 union type `T`에서 `string` 또는 `number` type만을 추출합니다.
- `boolean` type은 `never`로 대체되므로 결과적으로 추출되지 않습니다.


### 조건부 Type을 이용한 함수 Overloading 단순화

- 조건부 type을 사용하면, 복잡한 함수 overloading을 단순화할 수 있습니다.

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : T;

function f1(a: number): number;
function f1(a: string): string;
function f1(a: number | string) {
    return a;
}

type Test = ReturnType<typeof f1>;    // string | number
```

- `ReturnType`은 함수 type에서 반환 type을 추론합니다.
- `infer R` keyword를 사용하여 반환 type을 `R`로 추론하고, 이를 조건부 type의 결과로 사용합니다.
- 따라서 `Test` type은 `string | number` union type이 됩니다.




---




## Reference


- <https://www.typescriptlang.org/ko/docs/handbook/2/conditional-types.html>
- <https://typescript-kr.github.io/pages/advanced-types.html#조건부-type-conditional-types>






---
---
---
---
---
---
---
---
---
---







# TypeScript Advanced Type - Mapped Type (기존 Type으로 새로운 Type 생성하기)




## Mapped Type

- Mapped Type은 TypeScript에서 **기존의 type을 기반으로 새로운 type을 생성하는 방법을 제공**합니다.

- Mapped Type을 사용하면, **기존 type의 각 속성을 변형하여 새로운 type을 만들 수 있습니다.**
    - e.g., 속성을 읽기 전용으로 만들거나, 선택적으로 설정하거나, 심지어 속성 이름을 변형하는 등 다양한 용도로 사용될 수 있습니다.

```typescript
type MappedType<T> = {
    [P in keyof T]: T[P];
}
```

- `T`는 변형을 적용할 기존 type입니다.
- `keyof T`는 type `T`의 모든 속성 key를 union type으로 추출합니다.
- `P in keyof T`는 `T`의 각 속성에 대해 반복(iterate)하며, 각 속성 key `P`에 대해 적용할 변형을 정의합니다.
- `T[P]`는 속성 key `P`에 해당하는 type을 나타냅니다.


### 읽기 전용 Mapped Type

- 기존 type의 **모든 속성을 읽기 전용(readonly)으로 만듭니다.**

```typescript
interface Person {
    name: string;
    age: number;
}

type ReadonlyPerson = {
    readonly [P in keyof Person]: Person[P];
}

const person: ReadonlyPerson = {
    name: "John",
    age: 30
};

person.name = "Jane";    // 오류: 'name' 속성은 읽기 전용이므로 할당할 수 없습니다.
```


### 선택적 속성 Mapped Type

- 기존 type의 **모든 속성을 선택적(optional property)으로 만듭니다.**

```typescript
type PartialPerson = {
    [P in keyof Person]?: Person[P];
}

const person: PartialPerson = {
    name: "John"    // 'age' 속성은 선택적이므로 생략 가능
};
```




---




## Mapped Type 응용 : 다른 고급 Type과 함께 사용하기

- TypeScript에서 mapped type을 활용할 수 있는 방법과 응용 사례는 다양합니다.


### 조건부 Type과의 결합

- mapped type은 조건부 type과 결합하여 특정 조건에 따라 type을 다르게 할당할 수 있습니다.
    - e.g., 특정 type만 읽기 전용으로 만들고 싶을 때 사용할 수 있습니다.

```typescript
type ConditionalReadonly<T> = {
    [P in keyof T]: T[P] extends Function ? T[P] : Readonly<T[P]>;
};
```

- `T[P]`가 `Function` type을 상속한다면 그대로 두고, 그렇지 않은 경우에는 `Readonly<T[P]>`를 적용합니다.


### Template Literal Type과의 결합

- mapped type은 template literal type과 결합하여 속성의 이름을 동적으로 생성할 수 있습니다.
- 이를 통해 기존 type의 속성을 기반으로 새로운 속성 이름을 생성하는 것이 가능합니다.

```typescript
// 각 속성 이름 앞에 `get`을 붙이고 `Method`를 뒤에 붙여 새로운 메소드 이름 생성
type PropertyNames<T> = {
    [P in keyof T as `get${Capitalize<string & P>}Method`]: () => T[P]
};
```

```typescript
// 기존 type의 속성 이름에 접두사 추가
type PrefixedPerson = {
    [P in keyof Person as `prefixed_${string & P}`]: Person[P]
}

const person: PrefixedPerson = {
    prefixed_name: "John",
    prefixed_age: 30
};
```


### Utility Type 확장

- mapped type은 기존 utility type을 확장하거나 변형하여 새로운 utility type을 만드는 데 사용될 수 있습니다.
    - e.g., `Partial`과 `Readonly`를 결합한 새로운 utility type을 정의할 수 있습니다.

```typescript
type PartialReadonly<T> = {
    [P in keyof T]?: Readonly<T[P]>;
};
```

- `PartialReadonly` type은 객체의 모든 속성을 선택적이면서 동시에 읽기 전용으로 만듭니다.
















---
---
---
---
---
---
---
---
---
---















# TypeScript - Type Guard (Type 좁히기)



## Type Guard : Type 좁히기(Narrowing)

- type guard는 특정 scope 내에서 **변수의 type을 보다 구체적으로 좁히기 위해 사용**하는 표현식입니다.

- type guard를 사용하면 **특정 조건에서 변수가 특정 type임을 보장**할 수 있으며, 이를 통해 type 안정성을 높이고 runtime error를 줄일 수 있습니다.
    - 하지만 남용하면 코드의 복잡성이 증가할 수 있기 때문에, 필요한 경우에만 적절히 사용하는 것이 좋습니다.

- **`typeof`, `instanceof`, `in` keyword를 사용한 type guard**는 JavaScript에서 지원하는 기본적인 type과 class에 대해 사용될 수 있으며, 더 복잡한 type이나 interface에 대해서는 **'사용자 정의 type guard'**를 사용해야 합니다.




---




## `typeof` Keyword Type Guard

- `typeof` type guard는 기본적인 JavaScript type 검사(`string`, `number`, `boolean`, `symbol`, `undefined`, `object`, `function`)에 사용됩니다.
- 변수가 특정 기본 type일 때만 특정 logic을 실행하도록 할 수 있습니다.

```typescript
if (variable typeof type) {
    // 'variable'은 'type' type으로 간주됨
}
```


### `typeof` Keyword Type Guard 예제

```typescript
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

console.log(padLeft("Hello, world", 4));    // "    Hello, world"
console.log(padLeft("Hello, world", ">>> "));    // ">>> Hello, world"
```

```typescript
function getNumber(value: string | number): void {
    if (typeof value === "string") {
        console.log(value.length); 
    } else {
        console.log(value); 
    }
}
```




---




## `instanceof` Keyword Type Guard

- `instanceof` type guard는 class의 instance 여부를 검사할 때 사용됩니다.
- 변수가 특정 class의 instance인 경우에만 code block을 실행할 수 있습니다.

```typescript
if (instance instanceof Class) {
    // 'instance'는 'Class' type으로 간주됨
}
```

### `instanceof` Keyword Type Guard의 예제

```typescript
class Bird {
    fly() {
        console.log("bird flies");
    }
}

class Fish {
    swim() {
        console.log("fish swims");
    }
}

function move(pet: Bird | Fish): void {
    if (pet instanceof Bird) {
        pet.fly();
    }
    if (pet instanceof Fish) {
        pet.swim();
    }
}

const myBird = new Bird();
const myFish = new Fish();

move(myBird);    // bird flies
move(myFish);    // fish swims
```

```typescript
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}

class Animal {
    type: string;
    constructor(type: string) {
        this.type = type;
    }
}

function printDetails(obj: Person | Animal): void {
    if (obj instanceof Person) {
        console.log(obj.name);    // obj는 Person으로 추론됨
    } else {
        console.log(obj.type);    // obj는 Animal로 추론됨
    }
}
```




---




## `in` Keyword Type Guard

- `in` type guard는 객체가 특정 속성(property)을 가지고 있는지 여부를 검사할 때 사용됩니다.
- 특정 property가 객체 내에 존재하는 경우에만 code를 실행합니다.

```typescript
if ("property" in object) {
    // 'object'는 이 block 내에서 'property'를 가진 type으로 간주됨
}
```


### `in` Keyword Type Guard 예제

```typescript
interface Bird {
    fly(): void;
    layEggs(): void;
}

interface Fish {
    swim(): void;
    layEggs(): void;
}

function move(pet: Fish | Bird) {
    if ("swim" in pet) {
        pet.swim();
    } else {
        pet.fly();
    }
}

let pet: Fish = {swim() {}, layEggs() {}};
move(pet);    // 'pet.swim()'이 호출됨
```

```typescript
interface Book {
    id: number;
    rank: number;
}

interface OnlineLecture {
    id: string;
    url: string;
}

function learnCourse(material: Book | OnlineLecture) : number | string {
    if (rank in material) {
        return material.rank;    // material는 Book 추론됨
    } else {
        return material.url;    // obj는 OnlineLecture로 추론됨
    }
    
    if (id in material) {
    	console.log(material.id);
    }
}
```




---




## 사용자 정의 Type Guard

- 사용자 정의 type guard(user-defined type guard)는 개발자가 특정 조건을 직접 정의하여 변수의 type을 좁힐 수 있는 기능입니다.
    - `parameter is Type` 형태의 type 예측을 반환하는 함수로 구현합니다.

- type을 추론하는 함수(`isType`)가 참(`true`)을 반환할 때, 변수(`variable`)가 특정 type(`Type`)임을 compiler에게 알려주는 방식으로 작동합니다.
    - 함수가 `true`를 반환하면 `variable`이 `Type`이라고 type system(compiler)이 인식합니다.
    - `variable is Type`이 type을 추론(predicate)하는 부분입니다.

```typescript
function isType(variable: any): variable is Type {
    const result: boolean = true || false;
    return result;
}
```


### 사용자 정의 Type Guard 예제

```typescript
interface Bird {
    fly(): void;
}

interface Fish {
    swim(): void;
}

// 사용자 정의 type guard
function isFish(pet: Fish | Bird): pet is Fish {
    return (pet as Fish).swim !== undefined;
}

// 사용 예시
function move(pet: Fish | Bird) {
    if (isFish(pet)) {
        pet.swim();
    } else {
        pet.fly();
    }
}
```

```typescript
interface Car {
    brand: string;
    speed: number;
}

function isCar(vehicle: any): vehicle is Car {
    return "brand" in vehicle && "speed" in vehicle;
}

function displayVehicleInfo(vehicle: Car | { type: string }): void {
    if (isCar(vehicle)) {
        console.log(vehicle.brand, vehicle.speed);    // 'vehicle'은 'Car'로 추론됨
    } else {
        console.log(vehicle.type);    // 'vehicle'은 '{ type: string }'으로 추론됨
    }
}
```




---




## Reference

- <https://velog.io/@boyeon_jeong/%ED%83%80%EC%9E%85-%EA%B0%80%EB%93%9C>











---
---
---
---
---
---
---
---
---
---











# TypeScript - Optional Parameter (선택적 매개 변수)




## Optional Parameter : 필수가 아닌 매개 변수

- optional parameter(선택적 매개 변수)는 함수에 전달할 수도 있고 **생략할 수도 있는 매개 변수**입니다.
    - 더 유연한 함수 호출(overloading)이 가능하며, 매개 변수가 없는 경우는 함수 내부에서 따로 처리합니다.

- **매개 변수 이름 뒤에 `?` 기호를 추가**하여 optional parameter를 정의합니다.
    - optional parameter로 정의하면 해당 매개 변수를 전달하지 않아도 함수를 호출할 수 있습니다.

```typescript
function functionName(param: type, optionalParam?: type): returnType {
    // 함수 본문
}
```

- optional parameter 뒤에는 필수 매개 변수(일반 매개 변수)가 올 수 없습니다.
    - optional parameter는 **항상 필수 매개 변수 뒤에 위치**해야 합니다.

```typescript
function greet(name: string, age?: number): string {
    if (age === undefined) {
        return `Hello, ${name}!`;
    } else {
        return `Hello, ${name}! You are ${age} years old.`;
    }
}

console.log(greet("Alice"));    // Hello, Alice!
console.log(greet("Bob", 30));    // Hello, Bob! You are 30 years old.
```

- `age` 매개 변수는 선택적이므로, `greet` 함수는 `age` 매개 변수 없이 이름(`name`)만으로 호출할 수 있습니다.
- 함수 내부에서는 `age` 매개 변수가 `undefined`인지 확인하여, 매개 변수가 전달되지 않은 경우에 대해 다르게 처리할 수 있습니다.


### Optioanl Parameter에 값을 넣지 않았을 때 : `undefined`

- optional parameter에는 **기본적으로 `undefined` 값이 할당**됩니다.
    - optional parameter에 값을 할당하지 않고 함수를 호출하면, TypeScript는 해당 매개 변수의 값을 `undefined`로 처리합니다.
        - 그러나 매개 변수에 기본 값(default parameter)을 지정하는 경우엔, 매개 변수를 생략했을 때 `undefined` 대신 기본 값이 사용됩니다.

- optional parameter를 사용할 때는 항상 해당 매개 변수가 `undefined`일 가능성을 염두에 두고, 이에 대한 처리 logic을 구현해야 합니다.
    - optional parameter의 **`undefined` 값에 대한 type guard**는 함수의 안정성을 보장하고, 예상치 못한 오류를 방지하는 데 필수적입니다.

```typescript
function greet(name: string, greeting?: string) {
    if (greeting === undefined) {
        greeting = "Hello";    // 기본 값 설정
    }
    return `${greeting}, ${name}!`;
}

console.log(greet("Alice"));    // Hello, Alice!
console.log(greet("Bob", "Hi"));    // Hi, Bob!
```

- `greet` 함수는 두 번째 매개 변수인 `greeting`을 선택적으로 받습니다.
- `greeting` 매개 변수에 값이 제공되지 않은 경우, 함수 내부에서 이를 `undefined`로 간주하고 기본 값을 `"Hello"`로 설정합니다.
- `undefined`에 대한 typee guard를 통해 optional property를 생략했을 때도 함수가 예상대로 동작하도록 할 수 있습니다.




---




## Default Parameter : 더 안전한 Optional Parameter

- parameter에 기본 값을 설정하면, 함수 호출 시 **매개 변수를 생략했을 때 기본 값이 사용됩니다**.
    - **기본 값을 가진 매개 변수는 자동으로 optional parameter가 되기 때문에, 별도로 `?` 기호를 추가할 필요가 없습니다.**

- 기본 값이 없는 optional parameter와 마찬가지로, 기본 값을 가진 매개 변수는 **함수 매개 변수 목록의 끝에 위치**해야 합니다.

- **매개 변수 뒤에 `=` 연산자와 기본 값으로 지정할 값을 작성**하여 parameter에 기본 값을 설정합니다.

```typescript
function functionName(param: type = defaultValue, ...): returnType {
    // 함수 본문
}
```

```typescript
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

console.log(greet("Alice"));    //  Hello, Alice!
console.log(greet("Bob", "Hi"));    // Hi, Bob!
```

- `greeting` 매개 변수는 선택적으로 처리되며, 기본 값으로 `"Hello"`가 지정됩니다.
- 함수를 호출할 때 `greeting` 매개 변수를 생략하면, 기본 값 `"Hello"`가 사용됩니다.














---
---
---
---
---
---
---
---
---
---












# TypeScript - Optional Property (선택적 속성)




## Optional Property : 필수가 아닌 속성

- TypeScript에서 optional property(선택적 속성)는 **객체의 특정 속성이 필수가 아님**을 나타내는 방법입니다.
    - optional property를 가진 객체를 생성할 때, 해당 속성을 생략할 수 있습니다.

- optional property를 사용함으로써, type의 안정성을 유지하면서도 다양한 상황에 대응할 수 있는 유연한 code를 작성할 수 있습니다.

- optional property는 속성 이름 뒤에 **물음표(`?`)를 붙여 선언**합니다.

```typescript
interface User {
    id: number;
    name: string;
    email?: string;
    nickname?: string;
}

// email과 nickname 없이 User 객체 생성 가능
const user1: User = {
    id: 1,
    name: "John Doe"
};

// email과 nickname을 포함하여 User 객체 생성 가능
const user2: User = {
    id: 2,
    name: "Jane Doe",
    email: "jane@example.com",
    nickname: "Jane"
};
```

- `User` interface의 `email`과 `nickname`은 선택적 속성입니다.


### Optional Property의 기본값 : `undefined`

- optional property는 **정의되지 않았을 때 `undefined` 값**을 가집니다.
- 따라서, optional property를 사용할 때는 해당 속성이 `undefined`일 수 있다는 점을 고려하여 code를 작성해야 합니다.
    - `undefined` 값에 대한 **type guard**가 필요합니다.

```typescript
interface User {
    id: number;
    name: string;
    email?: string;
}

const user: User = {
    id: 1,
    name: "John Doe"
};

// optional property 접근
console.log(user.email);    // undefined

// optional property가 존재하는지 확인 후 사용 (type guard)
if (user.email) {
    console.log(user.email.toUpperCase());
} else {
    console.log("email 주소가 없습니다.");
}
```





---
---
---
---
---
---
---
---
---
---












# TypeScript - 읽기 전용 속성 (Read Only Property)




## `readonly` : 읽기 전용 속성

- TypeScript는 개발자가 class, interface, type alias에서 **property를 수정할 수 없게 만드는** `readonly` 한정자를 제공합니다.
- `readonly` 속성을 사용하면 객체 생성 후 해당 property의 값을 변경할 수 없습니다.
    - 이는 **불변성을 보장**하고, code를 더 안전하게 만들며, 의도하지 않은 변화로부터 보호하는 데 유용합니다.
    - 특히 함수형(functional) programming, 선언형(declarative) programming 등의 불변성을 중시하는 programming paradigm을 채택할 때 유용합니다.

- `readonly` 속성은 주로 class의 member 변수나 interface의 property에 사용됩니다.
    - class 생성자(constructor)에서 `readonly` property를 초기화할 수 있지만, 이후에는 그 값을 변경할 수 없습니다.


### `readonly` 속성을 갖는 Class

```typescript
class Person {
    readonly name: string;

    constructor(name: string) {
        this.name = name;    // 초기화는 가능함
    }

    changeName(newName: string) {
        this.name = newName;    // Error: Cannot assign to 'name' because it is a read-only property.
    }
}

const person = new Person("Alice");
console.log(person.name);    // "Alice"

person.name = "Bob";    // Error: Cannot assign to 'name' because it is a read-only property.
```

- `Person` class는 `readonly`로 선언된 `name` property를 갖고 있습니다.
    - `name` property는 객체 생성 시에만 값을 할당할 수 있으며, 이후에는 그 값을 변경할 수 없습니다.
    - 변경을 시도하면 TypeScript compiler는 오류를 발생시킵니다.


### `readonly` 속성을 갖는 Interface

```typescript
interface ReadonlyPerson {
    readonly name: string;
    readonly age: number;
}

const person: ReadonlyPerson = {
    name: "Alice",
    age: 30
};

person.name = "Bob";    // Error: Cannot assign to 'name' because it is a read-only property.
```

- `ReadonlyPerson` interface의 모든 property는 `readonly`로 선언되어 있어, 객체가 처음 생성될 때 이후에는 수정할 수 없습니다.




---




## `readonly`에 관한 Utility Type

- 대상의 모든 구성 요소에 일괄적으로 `readonly`를 적용하기 위해, `Readonly`, `ReadonlyArray` utility type을 사용할 수 있습니다.


### `ReadOnly<T>` : 객체 모든 속성을 `readonly`로 바꾸기

- 객체를 생성할 때 `Readonly` utility type을 사용하여, 모든 property를 `readonly`로 만들 수 있습니다.
    - 이 방식이 interface나 type alias에서 개별 property를 `readonly`로 만드는 것보다 더 간편할 수 있습니다.

```typescript
const obj: Readonly<{ property: string }> = { property: "value" };
obj.property = "another value";    // Error: Cannot assign to 'property' because it is a read-only property.
```

```typescript
interface Person {
    name: string;
    age: number;
}

const person: Readonly<Person> = {
    name: "Alice",
    age: 30
};

person.name = "Bob";    // Error: Cannot assign to 'name' because it is a read-only property.
```


### `ReadonlyArray<T>` : 배열의 모든 요소를 `readonly`로 바꾸기

- `readonly` 한정자는 배열(array)에도 사용될 수 있습니다.
- `ReadonlyArray<T>` utility type을 사용하면, 배열의 내용(항목)을 변경(추가, 삭제, 변경)할 수 없습니다.
    - 배열의 불변성을 강제합니다.

```typescript
let a: ReadonlyArray<number> = [1, 2, 3];

a.push(4);    // Error: Property 'push' does not exist on type 'readonly number[]'.
a[0] = 4;    // Error: Index signature in type 'readonly number[]' only permits reading.
```




---




## `const`와 `readonly`

- `const`와 `readonly`는 TypeScript 내에서 서로 보완적인 역할을 하며, data의 불변성을 관리하는 데 중요한 역할을 합니다.
    - `const`는 변수 자체에 적용되고, `readonly`는 객체의 속성에 적용됩니다.
    - `const`는 변수에 대한 binding을 불변으로 만들지만, `readonly`는 객체의 property를 변경할 수 없게 만듭니다.

- `readonly` 한정자는 변수 선언에 직접 사용될 수 없습니다.
    - `readonly`는 주로 객체의 property나 class의 member 변수의 불변성을 명시할 때 사용됩니다.
    - 변수에 대해서는 `const` 키워드를 사용하여 해당 변수가 재할당될 수 없음을 나타냅니다.


### 변수에 대한 `const` 사용

- 변수를 선언할 때 `const`를 사용하면, 그 변수에 다른 값을 재할당할 수 없습니다.
- 이는 변수의 불변성을 보장하지만, `const`로 선언된 객체의 내부 상태(property의 값)는 변경될 수 있습니다.

```typescript
const number = 42;
number = 43;    // Error: Cannot assign to 'number' because it is a constant.

const obj = { property: "value" };
obj.property = "new value";
obj = { anotherProperty: "value" };    // Error: Cannot assign to 'obj' because it is a constant.
```

- `obj` 객체의 내부 상태는 변경이 가능하나, 객체 자체는 변경할 수 없습니다.


### 객체 property에 대한 `readonly` 사용

- `readonly` 한정자는 객체의 property나 class member에 적용될 수 있으며, 이를 통해 해당 property의 값을 변경할 수 없게 만듭니다.
- `readonly`는 type을 정의할 때 사용됩니다.

```typescript
interface ReadonlyObject {
    readonly property: string;
}

let readonlyObj: ReadonlyObject = { property: "value" };
readonlyObj.property = "new value";    // Error: Cannot assign to 'property' because it is a read-only property.
```







