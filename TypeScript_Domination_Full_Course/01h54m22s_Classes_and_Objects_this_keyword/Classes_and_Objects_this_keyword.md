# Classes and Objects: The `this` Keyword

## Overview

Inside a class, `this` refers to the current instance of the class. It is used to access instance properties and methods from within the class. The value of `this` is determined by how a function is called, not where it is defined — this makes `this` one of the most misunderstood features in JavaScript and TypeScript.

---

## Basic Usage of `this`

Inside a method, `this` gives access to the instance's properties and other methods.

```typescript
class Person {
  constructor(public name: string, public age: number) {}

  describe(): string {
    return `${this.name} is ${this.age} years old`;
  }

  birthday(): void {
    this.age++;
  }
}

const p = new Person("Alice", 30);
console.log(p.describe()); // Alice is 30 years old
p.birthday();
console.log(p.age); // 31
```

```typescript
class Rectangle {
  constructor(public width: number, public height: number) {}

  area(): number {
    return this.width * this.height;
  }

  scale(factor: number): void {
    this.width *= factor;
    this.height *= factor;
  }
}

const r = new Rectangle(10, 5);
console.log(r.area()); // 50
r.scale(2);
console.log(r.area()); // 200
```

---

## Data Flow: `new` Call to `this.name`

When you write `new Person("Alice", 30)`, here is what happens:

1. A new empty object is created.
2. The constructor runs with `this` bound to that new object.
3. The parameter `public name: string` tells TypeScript to assign the argument to `this.name`.
4. Inside the constructor body, `this.name` and `this.age` are the instance properties.

```typescript
class User {
  constructor(
    public username: string,
    private password: string
  ) {
    // At this point: this.username = argument value
    //                this.password = argument value
    console.log(`Creating user: ${this.username}`);
  }
}

const u = new User("alice", "pass123");
// Output: Creating user: alice
```

```typescript
class Product {
  constructor(
    public name: string,
    public price: number
  ) {
    this.price = Math.max(0, price); // validation before assignment
  }
}

const p = new Product("Widget", -5);
console.log(p.price); // 0 (clamped to minimum)
```

---

## `this` in Methods

Methods defined with regular syntax receive `this` from the calling context.

```typescript
class Button {
  constructor(public label: string) {}

  onClick(): void {
    console.log(`${this.label} clicked`);
  }
}

const btn = new Button("Submit");
btn.onClick(); // this = btn instance -> "Submit clicked"
```

```typescript
class Calculator {
  constructor(public value: number = 0) {}

  add(n: number): this {
    this.value += n;
    return this; // return this for chaining
  }

  subtract(n: number): this {
    this.value -= n;
    return this;
  }

  getResult(): number {
    return this.value;
  }
}

const calc = new Calculator(10);
const result = calc.add(5).subtract(3).getResult();
console.log(result); // 12
```

---

## The `this` Problem: Losing Context

When a method is extracted from its object and called standalone, `this` can become `undefined` (in strict mode) or the global object.

```typescript
class Logger {
  constructor(public prefix: string) {}

  log(message: string): void {
    console.log(`[${this.prefix}] ${message}`);
  }
}

const logger = new Logger("APP");
const extracted = logger.log;
// extracted("Hello"); // Runtime error: Cannot read properties of undefined (reading 'prefix')
```

---

## Solutions to Lost `this`

### Arrow Function Property

Arrow functions capture `this` lexically from the surrounding scope at definition time.

```typescript
class SafeLogger {
  constructor(public prefix: string) {}

  log = (message: string): void => {
    console.log(`[${this.prefix}] ${message}`);
  };
}

const logger = new SafeLogger("APP");
const extracted = logger.log;
extracted("Hello"); // [APP] Hello
```

```typescript
class Counter {
  count: number = 0;

  increment = (): void => {
    this.count++;
  };
}

const c = new Counter();
const fn = c.increment;
fn();
console.log(c.count); // 1 (arrow function preserved this)
```

### `bind` in Constructor

Explicitly bind the method to the instance in the constructor.

```typescript
class BindExample {
  constructor(public name: string) {
    this.greet = this.greet.bind(this);
  }

  greet(): void {
    console.log(`Hi, ${this.name}`);
  }
}

const b = new BindExample("Alice");
const fn = b.greet;
fn(); // Hi, Alice
```

---

## `this` with Access Modifiers

Access modifiers affect visibility but do not change how `this` behaves.

```typescript
class Account {
  constructor(
    public owner: string,
    private balance: number
  ) {}

  showBalance(this: Account): void {
    console.log(`${this.owner} has $${this.balance}`);
  }

  private encrypt(data: string): string {
    return data.split("").reverse().join("");
  }

  getEncryptedData(): string {
    return this.encrypt(this.balance.toString()); // private method accessible via this
  }
}

const acc = new Account("Alice", 1000);
acc.showBalance(); // Alice has $1000
// acc.encrypt("x"); // Error: private
```

---

## `this` Parameter in TypeScript

TypeScript lets you declare an explicit `this` parameter as the first parameter of a method. It is a compile-time check only (removed at runtime).

```typescript
class ClickHandler {
  constructor(public element: string) {}

  handleClick(this: ClickHandler): void {
    console.log(`${this.element} clicked`);
  }
}

const handler = new ClickHandler("Button");
handler.handleClick(); // OK

const fn = handler.handleClick;
// fn(); // TypeScript error: The 'this' context of type 'void' is not assignable to method's 'this' of type 'ClickHandler'
```

```typescript
class EventEmitter {
  constructor(public name: string) {}

  onEvent(this: EventEmitter, callback: () => void): void {
    console.log(`${this.name} emitting event`);
    callback();
  }
}
```

---

## `this` in Inheritance

When a derived class calls a method inherited from a base class, `this` refers to the derived class instance.

```typescript
class Animal {
  constructor(public name: string) {}

  speak(this: Animal): void {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  constructor(name: string, public breed: string) {
    super(name);
  }

  speak(this: Dog): void {
    console.log(`${this.name} barks (${this.breed})`);
  }
}

const d = new Dog("Rex", "Husky");
d.speak(); // Rex barks (Husky)
```

```typescript
class Base {
  constructor(public value: number) {}

  getValue(): number {
    return this.value;
  }
}

class Derived extends Base {
  constructor(value: number, public multiplier: number) {
    super(value);
  }

  getComputed(): number {
    return this.getValue() * this.multiplier; // this.getValue() uses Derived's this
  }
}

const d = new Derived(10, 3);
console.log(d.getComputed()); // 30
```

---

## The Shortcut Method vs Arrow Method

```typescript
class Demo {
  value: string = "hello";

  // Shortcut method — on prototype, this depends on call site
  shortcut(): void {
    console.log(this.value);
  }

  // Arrow method — on instance, this is always the instance
  arrowMethod = (): void => {
    console.log(this.value);
  };
}

const d = new Demo();
const s = d.shortcut;
const a = d.arrowMethod;

// s(); // Runtime error: this is undefined
a(); // "hello" (arrow preserves this)
```

```typescript
class Toggle {
  isOn: boolean = false;

  toggle = (): boolean => {
    this.isOn = !this.isOn;
    return this.isOn;
  };
}

const t = new Toggle();
const toggleFn = t.toggle;
console.log(toggleFn()); // true (works because arrow function)
```

---

## Why `this` Is Needed

Without `this`, methods would not know which object they belong to. `this` connects a method to the specific instance that invoked it.

```typescript
class Player {
  constructor(public name: string, public score: number = 0) {}

  addPoints(points: number): void {
    this.score += points;
  }
}

const p1 = new Player("Alice");
const p2 = new Player("Bob");
p1.addPoints(10); // this = p1
p2.addPoints(20); // this = p2
console.log(p1.score); // 10
console.log(p2.score); // 20
```

---

## Tricky Things to Remember

1. **`this` is determined by call site, not definition** — A method extracted from its object loses its `this` binding unless you use arrow functions, `bind`, or a `this` parameter.

2. **Arrow functions in classes create per-instance copies** — Each instance gets its own copy of the arrow function, increasing memory usage. Regular methods live on the prototype once.

```typescript
class Comparison {
  method() {}
  arrow = () => {};
}
const a = new Comparison();
const b = new Comparison();
console.log(a.method === b.method); // true (prototype)
console.log(a.arrow === b.arrow);   // false (each instance has its own)
```

3. **`this` parameter is a TypeScript-only feature** — It is erased at runtime but provides compile-time safety against incorrect `this` usage.

4. **`this` inside a static method refers to the class constructor itself**, not an instance.

```typescript
class MyClass {
  static x: number = 10;
  static getX(): number {
    return this.x;
  }
}
console.log(MyClass.getX()); // 10
```

5. **Event handlers and callbacks are common places to lose `this`** — Always use arrow functions or `bind` when passing class methods as callbacks.

---

## Analogies

- **`this` as a nametag**: When you walk into a room, you pin on a nametag so everyone knows who you are. `this` is the nametag the method wears — it tells the method which object it is talking about.
- **`this` as a remote control**: A universal remote (method) can control any TV (object), but you must point it at a specific TV. `this` is the TV the remote is currently pointing at.

---

## Interview Questions

### Q1: What does `this` refer to inside a class method?

Inside a regular class method, `this` refers to the instance that the method was called on. It is determined by the call site, not where the method is defined.

```typescript
class Demo {
  value: string = "hello";
  show(): string {
    return this.value;
  }
}
const d = new Demo();
console.log(d.show()); // "hello" — this = d
```

### Q2: Why does `this` become `undefined` when extracting a method?

When a method is extracted from its object and called standalone, the `this` context is lost. In strict mode (which TypeScript enables), `this` becomes `undefined`. In non-strict mode, it would fall back to the global object.

```typescript
class Widget {
  label: string = "Click me";
  handle(): void { console.log(this.label); }
}
const w = new Widget();
const fn = w.handle;
// fn(); // TypeError: Cannot read properties of undefined
```

### Q3: How do arrow functions solve the `this` problem?

Arrow functions capture `this` from the surrounding lexical scope at definition time, not at call time. When defined in a class, they capture the class instance's `this` permanently.

```typescript
class SafeWidget {
  label: string = "Click me";
  handle = (): void => {
    console.log(this.label); // this is always the instance
  };
}
const w = new SafeWidget();
const fn = w.handle;
fn(); // Click me
```

### Q4: What is the difference between a regular method and an arrow function as a class property?

A regular method lives on the prototype and is shared across all instances. An arrow function is a class field — it is created per instance, consuming more memory, but it correctly captures `this` regardless of call site.

```typescript
class Compare {
  regular() { return this; }
  arrow = () => { return this; };
}
const c = new Compare();
console.log(c.regular === new Compare().regular); // true
console.log(c.arrow === new Compare().arrow);     // false
```

### Q5: What is the `this` parameter in TypeScript?

The `this` parameter is a compile-time annotation as the first parameter of a method. It lets you specify what type `this` should be when the method is called. It is erased at runtime.

```typescript
class Handler {
  data: string = "test";
  process(this: Handler): void {
    console.log(this.data);
  }
}
const h = new Handler();
// const fn = h.process;
// fn(); // TypeScript error: 'this' context mismatch
```

### Q6: How does `this` behave in inherited classes?

`this` always refers to the instance that the method was called on, regardless of whether the method is defined in the base class or the derived class. This enables polymorphism.

```typescript
class Base {
  constructor(public name: string) {}
  greet(): string {
    return `Hi from ${this.name}`;
  }
}
class Derived extends Base {
  constructor(name: string, public extra: string) {
    super(name);
  }
}
const d = new Derived("Alice", "extra");
console.log(d.greet()); // Hi from Alice — this = Derived instance
```

### Q7: What is `bind` and how does it relate to `this`?

`bind` creates a new function with a fixed `this` value. When you extract a method, you can `bind` it to the instance so it works correctly.

```typescript
class Logger {
  prefix: string = "LOG";
  log(message: string): void {
    console.log(`[${this.prefix}] ${message}`);
  }
}
const logger = new Logger();
const boundLog = logger.log.bind(logger);
boundLog("Hello"); // [LOG] Hello
```

### Q8: What does `this` refer to in a static method?

In a static method, `this` refers to the class constructor function itself, not an instance.

```typescript
class Config {
  static defaultPort: number = 3000;
  static getPort(): number {
    return this.defaultPort;
  }
}
console.log(Config.getPort()); // 3000
```

### Q9: Can you use `this` in a constructor parameter property?

No, `this` is not available inside constructor parameter property expressions. Parameter properties are a shorthand for declaring and assigning, and `this` is not needed there.

```typescript
// Valid: this in constructor body
class Valid {
  constructor(public name: string) {
    console.log(this.name);
  }
}
```

### Q10: What happens to `this` when a method is passed as a callback to a DOM event listener?

The DOM event listener calls the callback with the DOM element as `this`. To preserve the class instance, use an arrow function or `bind`.

```typescript
class UIComponent {
  element: HTMLElement;
  constructor(public label: string) {
    this.element = document.createElement("button");
    // This would lose context:
    // this.element.addEventListener("click", this.handleClick);
    // This works:
    this.element.addEventListener("click", () => this.handleClick());
  }

  handleClick(): void {
    console.log(`${this.label} clicked`);
  }
}
```
