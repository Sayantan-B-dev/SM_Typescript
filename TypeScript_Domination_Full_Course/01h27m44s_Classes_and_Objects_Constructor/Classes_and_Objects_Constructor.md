# Classes and Objects: Constructor

## Overview

The constructor is a special method that runs automatically when a new object is created with `new`. Its primary role is to initialize the new instance with values passed as arguments, giving each object its own distinct data. Without a constructor, every instance would have the same default values — the constructor is what makes each object unique.

---

## Role of the Constructor

The constructor gives an object its initial shape and data. It receives parameters from the `new` call and assigns them to instance properties.

```typescript
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const alice = new Person("Alice", 30);
const bob = new Person("Bob", 25);
console.log(alice.name); // Alice
console.log(bob.age);    // 25
```

**Analogy**: A constructor is like an assembly line worker who receives raw materials (parameters) and assembles a unique product (object) according to the blueprint (class). The class defines what parts exist; the constructor fills them with specific values.

---

## Constructor Parameter Properties

TypeScript provides a shorthand: prefix a constructor parameter with `public`, `private`, `protected`, or `readonly` to automatically create and initialize a property of the same name.

```typescript
class User {
  constructor(
    public username: string,
    public email: string,
    private password: string
  ) {}
}

const u = new User("alice", "a@b.com", "secret123");
console.log(u.username); // alice
console.log(u.email);    // a@b.com
// console.log(u.password); // Error: Property 'password' is private
```

Without parameter properties, the equivalent code is:

```typescript
class UserLong {
  username: string;
  email: string;
  private password: string;

  constructor(username: string, email: string, password: string) {
    this.username = username;
    this.email = email;
    this.password = password;
  }
}
```

---

## Default Property Values vs Constructor Assignment

You can set default values directly on the property declaration. These run before the constructor body. If the constructor assigns a value, it overwrites the default.

```typescript
class Article {
  title: string = "Untitled";
  wordCount: number = 0;

  constructor(title?: string, wordCount?: number) {
    if (title) this.title = title;
    if (wordCount !== undefined) this.wordCount = wordCount;
  }
}

const a1 = new Article();
console.log(a1.title); // Untitled

const a2 = new Article("TypeScript Guide", 1500);
console.log(a2.title); // TypeScript Guide
```

**Why set defaults at declaration instead of in the constructor parameter?**

Property initializers run before the constructor, making the default available even if the constructor does not assign that property. They also make the intent clearer and work with `readonly` properties that must be initialized at declaration or in the constructor.

```typescript
class Timer {
  readonly startTime: number = Date.now(); // works
  elapsed: number = 0;

  constructor() {
    // startTime is already set
  }
}
```

---

## Constructor with Different Parameter Patterns

### Optional Parameters

```typescript
class Config {
  host: string;
  port: number;
  debug: boolean;

  constructor(host: string, port?: number, debug: boolean = false) {
    this.host = host;
    this.port = port ?? 8080;
    this.debug = debug;
  }
}

const c1 = new Config("localhost");
console.log(c1.port); // 8080
const c2 = new Config("example.com", 443, true);
```

### Multiple Constructors (Overloads)

TypeScript does not support multiple constructor implementations, but you can define overload signatures.

```typescript
class Vector2D {
  x: number;
  y: number;

  constructor(x: number, y: number);
  constructor(obj: { x: number; y: number });
  constructor(arg1: number | { x: number; y: number }, arg2?: number) {
    if (typeof arg1 === "object") {
      this.x = arg1.x;
      this.y = arg1.y;
    } else {
      this.x = arg1;
      this.y = arg2!;
    }
  }
}

const v1 = new Vector2D(3, 4);
const v2 = new Vector2D({ x: 5, y: 6 });
```

---

## Access Modifiers in Constructor Parameters

Using `public`, `private`, or `protected` in constructor parameters auto-declares the property with that visibility.

```typescript
class Employee {
  constructor(
    public name: string,
    private salary: number,
    protected department: string
  ) {}

  getSalary(): number {
    return this.salary; // accessible internally
  }
}

class Manager extends Employee {
  constructor(name: string, salary: number, department: string) {
    super(name, salary, department);
    console.log(this.department); // accessible (protected)
  }
}

const emp = new Employee("Alice", 70000, "Engineering");
console.log(emp.name);        // Alice (public)
// console.log(emp.salary);   // Error: private
// console.log(emp.department); // Error: protected
```

---

## Changing Object Without Constructor

If a class has no explicit constructor, JavaScript provides a default constructor that takes no arguments. Properties must be assigned after instantiation.

```typescript
class Car {
  brand: string = "";
  year: number = 0;
}

const myCar = new Car();
myCar.brand = "Honda";
myCar.year = 2021;
```

The problem with this approach: anyone can change any public property at any time. There is no encapsulation or validation.

```typescript
class MutableAccount {
  balance: number = 0;
}

const acc = new MutableAccount();
acc.balance = 100; // fine
acc.balance = -500; // also fine — no protection
```

Using a constructor with validation solves this:

```typescript
class SafeAccount {
  private _balance: number = 0;

  constructor(initialBalance: number) {
    if (initialBalance < 0) {
      throw new Error("Initial balance cannot be negative");
    }
    this._balance = initialBalance;
  }

  getBalance(): number {
    return this._balance;
  }
}
```

---

## Parameter Properties vs Declared Properties

When to use `public` in the constructor vs declaring a property in the class body:

```typescript
// Constructor parameter property — concise, auto-declares
class Shortcut {
  constructor(public value: string) {}
}

// Declared property + constructor assignment — verbose
class Verbose {
  value: string;
  constructor(value: string) {
    this.value = value;
  }
}

// Declared property with logic — use class body declaration
class WithLogic {
  value: string;
  constructor(raw: string) {
    this.value = raw.trim().toLowerCase();
  }
}
```

Use parameter properties when you simply accept a value and assign it directly. Use a class body declaration + manual assignment when you need to transform the value or perform additional logic.

---

## Edge Cases and Hidden Behaviors

### Returning a Non-Primitive from Constructor

If a constructor returns an object, that object becomes the result of `new`, not `this`.

```typescript
class Weird {
  constructor() {
    return { notTheInstance: true };
  }
}
const w = new Weird();
// w is the plain object, not a Weird instance
console.log(w.notTheInstance); // true
// console.log(w instanceof Weird); // false
```

### Constructor with `readonly`

A `readonly` property can be set in the constructor but not afterward.

```typescript
class ReadonlyExample {
  readonly id: string;
  constructor(id: string) {
    this.id = id;
  }
}
```

### Constructor Parameter Defaults

Setting defaults directly on constructor parameters is fine, but property initializers in the class body run first:

```typescript
class Order {
  items: number = 0; // runs first
  constructor(items?: number) {
    if (items !== undefined) {
      this.items = items;
    }
  }
}
```

---

## Tricky Things to Remember

1. **The constructor is optional** — If you do not define one, a parameterless empty constructor is automatically provided.

2. **Parameter properties and explicit property declarations cannot overlap** — You cannot declare `public name: string` in the class body and also use `constructor(public name: string)`.
```typescript
// Error: Duplicate identifier
class Duplicate {
  name: string;
  constructor(public name: string) {}
}
```

3. **Property initializers run before the constructor body** — They execute in declaration order. This matters when a default value depends on another property.
```typescript
class InitOrder {
  a: number = 1;
  b: number = this.a + 1; // 2
  constructor() {
    this.a = 10;
    // b is already set to 2, not 11
  }
}
```

4. **A constructor cannot have type parameters** — Generics on a class itself are fine, but the constructor cannot introduce new type parameters.

5. **Overload signatures must all have the same implementation** — You can declare multiple constructor signatures, but only one implementation body handles them all.

---

## Analogies

- **Constructor as a birth certificate**: The class defines that every person has a name and birth date. The constructor fills in the actual values when a person is born.
- **Constructor as a coffee machine**: The machine (class) has slots for water, beans, and milk. The constructor is the act of putting specific ingredients in — you get a different drink each time.

---

## Interview Questions

### Q1: What is the purpose of a constructor in TypeScript?

The constructor initializes a newly created instance. It receives arguments from the `new` call and assigns them to instance properties, so each object can have its own unique data. It runs automatically when `new` is invoked.

```typescript
class Bottle {
  capacity: number;
  constructor(capacity: number) {
    this.capacity = capacity;
  }
}
const small = new Bottle(250);
const large = new Bottle(1000);
```

### Q2: What are constructor parameter properties?

A TypeScript feature where you prefix a constructor parameter with `public`, `private`, `protected`, or `readonly`. This automatically declares a property on the class and assigns the parameter value to it, eliminating boilerplate.

```typescript
class Point {
  constructor(public x: number, public y: number) {}
}
// Equivalent to:
class PointLong {
  x: number;
  y: number;
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}
```

### Q3: Can you have multiple constructors in TypeScript?

No, you can only have one constructor implementation. However, you can use constructor overload signatures to provide multiple type-safe call signatures that all resolve to a single implementation.

```typescript
class Message {
  text: string;
  constructor(text: string);
  constructor(bytes: Uint8Array);
  constructor(input: string | Uint8Array) {
    this.text = typeof input === "string" ? input : new TextDecoder().decode(input);
  }
}
```

### Q4: Why might you set a default value on a property declaration instead of in the constructor parameter?

Property initializers run before the constructor body, so the default is available even if the constructor logic skips that property. They also work naturally with `readonly` properties and make the default visible at a glance.

```typescript
class Logger {
  readonly createdAt: Date = new Date(); // runs before constructor
  level: string = "info";
  constructor(level?: string) {
    if (level) this.level = level;
  }
}
```

### Q5: What happens if a constructor returns an object?

If the constructor explicitly returns a non-primitive object, that object replaces the newly created instance as the result of `new`. The `this` object is discarded.

```typescript
class ReturnsObject {
  constructor() {
    return { surprise: true };
  }
}
const r = new ReturnsObject();
console.log(r.surprise); // true
console.log(r instanceof ReturnsObject); // false
```

### Q6: What is the difference between `public`, `private`, and `protected` in constructor parameters?

- `public`: the property is accessible anywhere (default).
- `private`: the property is only accessible within the class it is defined in.
- `protected`: the property is accessible within the class and its subclasses.

```typescript
class Base {
  constructor(public a: number, private b: number, protected c: number) {}
}
class Derived extends Base {
  constructor(a: number, b: number, c: number) {
    super(a, b, c);
    console.log(this.a); // OK (public)
    // console.log(this.b); // Error (private)
    console.log(this.c); // OK (protected)
  }
}
```

### Q7: Can the constructor be async?

No, a constructor cannot be `async`. If you need asynchronous initialization, use a static factory method or an `init()` method called after construction.

```typescript
class AsyncInit {
  data: string = "";

  private constructor() {}

  static async create(): Promise<AsyncInit> {
    const instance = new AsyncInit();
    instance.data = await fetchData();
    return instance;
  }
}

async function fetchData(): Promise<string> {
  return "loaded";
}
```

### Q8: What is the order of initialization when a class has both property defaults and a constructor?

1. The prototype is set up.
2. Properties with default values are assigned in declaration order.
3. The constructor body executes (assigning or overwriting properties).

```typescript
class OrderTest {
  first: number = 1;
  second: number = this.first + 1;
  constructor() {
    this.first = 100;
    // second was already set to 2, now it stays 2
  }
}
const o = new OrderTest();
console.log(o.first);  // 100
console.log(o.second); // 2
```

### Q9: What is the `super()` call in a derived class constructor?

`super()` must be called in the constructor of a derived class before accessing `this`. It invokes the parent class constructor to initialize the parent's properties.

```typescript
class Animal {
  constructor(public name: string) {}
}
class Dog extends Animal {
  constructor(name: string, public breed: string) {
    super(name); // must be called first
  }
}
const d = new Dog("Rex", "Husky");
```

### Q10: What are the pitfalls of using parameter properties with access modifiers?

The main pitfall is accidentally exposing internal state through `public` when you meant `private`. Another is forgetting that a parameter property creates both a parameter and a property, so you cannot also declare the property separately.

```typescript
// Exposes internal state accidentally
class Unsafe {
  constructor(public secretKey: string) {} // now visible everywhere
}

// Duplicate declaration error
// class Bad {
//   name: string;
//   constructor(public name: string) {} // Error
// }
```
