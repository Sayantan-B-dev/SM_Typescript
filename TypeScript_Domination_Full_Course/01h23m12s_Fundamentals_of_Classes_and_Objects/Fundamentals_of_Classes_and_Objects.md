# Fundamentals of Classes and Objects

## Overview

A class in TypeScript is a blueprint for creating objects. It encapsulates data (properties) and behavior (methods) into a single unit. Classes are a core part of object-oriented programming (OOP) and allow you to model real-world entities, reuse code through inheritance, and enforce contracts through interfaces. TypeScript builds on JavaScript's class syntax by adding type annotations, access modifiers (public, private, protected), abstract classes, and parameter properties.

---

## Creating a Class

Use the `class` keyword followed by the class name. Inside the class body, you define properties and methods.

```typescript
class Person {
  name: string;
  age: number;

  greet(): void {
    console.log(`Hello, I am ${this.name}`);
  }
}
```

```typescript
// Create an instance of the class
const p1 = new Person();
p1.name = "Alice";
p1.age = 30;
p1.greet(); // Hello, I am Alice
```

---

## Using the `new` Keyword

The `new` keyword creates a new object based on the class. Each call to `new` produces a fresh independent instance.

```typescript
class Counter {
  count: number = 0;

  increment(): void {
    this.count++;
  }
}

const c1 = new Counter();
const c2 = new Counter();
c1.increment();
console.log(c1.count); // 1
console.log(c2.count); // 0
```

```typescript
class Product {
  name: string = "Default";
  price: number = 0;
}

const itemA = new Product();
const itemB = new Product();
itemA.name = "Laptop";
itemB.name = "Mouse";
console.log(itemA.name); // Laptop
console.log(itemB.name); // Mouse
```

---

## Class Properties with Default Values

You can assign default values directly in the class body. Every new instance starts with these defaults.

```typescript
class Settings {
  theme: string = "light";
  fontSize: number = 14;
  notifications: boolean = true;
}

const s = new Settings();
console.log(s.theme); // light
s.theme = "dark";
console.log(s.theme); // dark
```

```typescript
class GameCharacter {
  health: number = 100;
  mana: number = 50;
  level: number = 1;
}

const hero = new GameCharacter();
hero.health = 75;
console.log(hero.health); // 75
console.log(hero.mana);   // 50 (unchanged)
```

---

## Methods on Classes

Methods are functions defined inside the class body. They have access to `this` which refers to the instance.

```typescript
class Rectangle {
  width: number = 0;
  height: number = 0;

  area(): number {
    return this.width * this.height;
  }

  isSquare(): boolean {
    return this.width === this.height;
  }
}

const rect = new Rectangle();
rect.width = 10;
rect.height = 10;
console.log(rect.area());     // 100
console.log(rect.isSquare()); // true
```

```typescript
class BankAccount {
  balance: number = 0;

  deposit(amount: number): void {
    this.balance += amount;
  }

  withdraw(amount: number): boolean {
    if (amount <= this.balance) {
      this.balance -= amount;
      return true;
    }
    return false;
  }
}

const account = new BankAccount();
account.deposit(500);
console.log(account.withdraw(200)); // true
console.log(account.balance);       // 300
```

---

## Why Every Object from a Class Starts with the Same Shape

A class defines a fixed structure (properties and their types). Every instance created with `new` gets that same structure, but each instance stores its own independent values.

```typescript
class Point {
  x: number = 0;
  y: number = 0;
}

const p1 = new Point();
const p2 = new Point();
// p1 and p2 have the same shape: { x: number; y: number }
// But their values can differ independently
p1.x = 5;
p2.x = 10;
```

```typescript
class User {
  username: string = "";
  email: string = "";
  isActive: boolean = false;
}

const users: User[] = [];
for (let i = 0; i < 3; i++) {
  const u = new User();
  u.username = `user${i}`;
  users.push(u);
}
console.log(users[0].username); // user0
console.log(users[1].username); // user1
```

---

## Using Class as a Type

A class name can be used as a type annotation for variables and function parameters.

```typescript
class Car {
  brand: string = "";
  year: number = 0;
}

function printCarInfo(car: Car): void {
  console.log(`${car.brand} (${car.year})`);
}

const myCar = new Car();
myCar.brand = "Toyota";
myCar.year = 2020;
printCarInfo(myCar); // Toyota (2020)
```

```typescript
class Task {
  title: string = "";
  completed: boolean = false;
}

function isTaskComplete(task: Task): boolean {
  return task.completed;
}

const t = new Task();
t.title = "Write docs";
t.completed = true;
console.log(isTaskComplete(t)); // true
```

---

## Class vs Plain Object

A class provides a reusable blueprint; a plain object literal is a one-off.

```typescript
// Plain object — no reuse
const obj1 = { x: 1, y: 2 };
const obj2 = { x: 3, y: 4 }; // duplicate definition

// Class — reusable blueprint
class Coord {
  x: number = 0;
  y: number = 0;
}
const c1 = new Coord();
const c2 = new Coord();
```

```typescript
// Methods on a class live on the prototype, shared across instances
class Greeter {
  name: string = "";

  sayHi(): string {
    return `Hi, ${this.name}`;
  }
}

const g1 = new Greeter();
const g2 = new Greeter();
console.log(g1.sayHi === g2.sayHi); // true (shared method)
```

---

## Tricky Things to Remember

1. **Properties must be declared** — Unlike plain objects, you cannot dynamically add properties to a class instance unless they are declared in the class body.
```typescript
class Foo { a: number = 1; }
const f = new Foo();
// f.b = 2; // Error: Property 'b' does not exist on type 'Foo'
```

2. **Default values are evaluated per instance** — If you assign a mutable object as a default, every instance shares the same reference.
```typescript
class Shared {
  items: number[] = [];
}
const s1 = new Shared();
const s2 = new Shared();
s1.items.push(1);
console.log(s2.items.length); // 0 (each instance gets its own array)
```

3. **Methods are on the prototype** — Methods defined in the class body are added to the prototype, not the instance itself. Arrow functions in class properties (class fields) are per-instance.
```typescript
class Example {
  method() {}           // on prototype
  arrow = () => {};     // on instance (new copy per new)
}
```

4. **Class type inference** — TypeScript infers the instance type from the class, even without explicit annotation.

5. **Initialization order** — Property initializers run in order of declaration, top to bottom, before the constructor body executes.

---

## Analogies

- **Class as a cookie cutter**: The class is the metal cutter shape. Every time you press it (`new`), you get a cookie with that exact shape, but you can decorate each cookie differently (different property values).
- **Class as a blueprint for a house**: The blueprint defines rooms, doors, and windows. Every house built from that blueprint has the same layout, but each house can have different paint colors and furniture.

---

## Interview Questions

### Q1: What is the difference between a class and an object in TypeScript?

A class is a compile-time blueprint or template that defines structure and behavior. An object is a runtime instance created from that class. A class exists in source code; an object exists in memory with its own property values.

```typescript
class Animal {
  name: string = "";
  speak(): void {
    console.log(`${this.name} makes a sound`);
  }
}
// Animal is the class (blueprint)
const dog = new Animal(); // dog is the object (instance)
dog.name = "Rex";
```

### Q2: What happens when you use the `new` keyword on a class?

The `new` keyword:
1. Creates a new empty plain object.
2. Sets the prototype of that object to the class's `prototype` property.
3. Binds `this` inside the constructor to the new object.
4. Executes the constructor body (if one exists) to initialize properties.
5. Returns the new object (unless the constructor returns a non-primitive, in which case that is returned instead).

```typescript
class Demo {
  value: number = 42;
}
const d = new Demo();
console.log(d.value); // 42
```

### Q3: Can you create an object without a class in TypeScript?

Yes, using object literals. However, object literals are singletons — they do not provide a reusable blueprint. Classes are the preferred way to create many objects with the same structure.

```typescript
// Object literal — one-off
const point = { x: 10, y: 20 };

// Class — reusable
class Point {
  x: number = 0;
  y: number = 0;
}
const p1 = new Point();
const p2 = new Point();
```

### Q4: Why does every object created from the same class start with the same shape?

Because the class definition declares the set of properties and their types. Every time you instantiate the class, JavaScript allocates those same properties on the new object (with default values if provided). This ensures type safety and predictability.

```typescript
class Config {
  host: string = "localhost";
  port: number = 3000;
  debug: boolean = false;
}
const c1 = new Config();
const c2 = new Config();
// c1 and c2 have identical shape, but independent values
c1.port = 8080;
console.log(c2.port); // 3000
```

### Q5: What is the difference between a method defined in the class body and an arrow function property?

A method defined with the method syntax (`foo() {}`) is added to the class prototype — shared across all instances. An arrow function assigned to a property (`foo = () => {}`) is created anew for each instance, which consumes more memory but captures the lexical `this` correctly.

```typescript
class Compare {
  method() { return this; }
  arrow = () => { return this; };
}
const c = new Compare();
console.log(c.hasOwnProperty("method")); // false (on prototype)
console.log(c.hasOwnProperty("arrow"));  // true (on instance)
```

### Q6: Can a class have properties without type annotations?

Yes, but TypeScript will infer the type from the default value, or default to `any` if no value is given and `noImplicitAny` is disabled. It is best practice to always annotate properties.

```typescript
class WithoutAnnotation {
  name = "hello";        // inferred as string
  age;                   // any (if noImplicitAny is off)
}
```

### Q7: What is the difference between a class and an interface?

A class provides an implementation — it can have property defaults, method bodies, and constructors. An interface is a purely structural contract with no implementation. A class can `implement` an interface to guarantee it meets the contract.

```typescript
interface Flyable {
  fly(): void;
}

class Bird implements Flyable {
  fly(): void {
    console.log("Flapping wings");
  }
}
```

### Q8: Can you use the `readonly` modifier on class properties?

Yes. A `readonly` property can be assigned only during declaration or in the constructor. After that, it cannot be reassigned.

```typescript
class ImmutablePoint {
  readonly x: number;
  readonly y: number;

  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}

const ip = new ImmutablePoint(5, 10);
// ip.x = 20; // Error: Cannot assign to 'x' because it is a read-only property
```

### Q9: What is the `implements` clause in TypeScript classes?

The `implements` clause forces a class to satisfy a specific interface. If the class is missing a required property or method, TypeScript reports a compile-time error.

```typescript
interface Identifiable {
  id: string;
  getId(): string;
}

class Entity implements Identifiable {
  constructor(public id: string) {}
  getId(): string {
    return this.id;
  }
}
```

### Q10: Does TypeScript support multiple class inheritance?

No, TypeScript (like JavaScript) does not support multiple inheritance — a class can only extend one other class. However, you can achieve similar behavior through interfaces (a class can implement multiple interfaces) and mixins.

```typescript
interface A { a(): void; }
interface B { b(): void; }
class MyClass implements A, B {
  a(): void { console.log("A"); }
  b(): void { console.log("B"); }
}
```
