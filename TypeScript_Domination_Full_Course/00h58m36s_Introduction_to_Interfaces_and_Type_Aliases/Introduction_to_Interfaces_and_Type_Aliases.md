# Introduction to Interfaces and Type Aliases

## Overview

Interfaces and type aliases are TypeScript's primary tools for defining reusable object shapes, contracts, and custom types. They both allow you to define the structure that values must conform to, but they have important differences.

## Interfaces

An interface defines the shape of an object -- what properties it must have and their types.

```typescript
interface User {
    name: string;
    email: string;
    age: number;
}

function registerUser(user: User): void {
    console.log(`Registering ${user.name} with email ${user.email}`);
}

// Correct usage -- object matches the interface exactly
registerUser({
    name: "Alice",
    email: "alice@example.com",
    age: 25
});

// Error: missing required property 'email'
// registerUser({
//     name: "Bob",
//     age: 30
// });

// Error: extra property 'phone'
// registerUser({
//     name: "Charlie",
//     email: "charlie@example.com",
//     age: 35,
//     phone: "123-456-7890"
// });
```

## Expanded Code Examples

### Example 1: Interface with Optional and Readonly Properties

```typescript
interface Employee {
    readonly id: number;      // Cannot be changed after assignment
    name: string;
    email: string;
    phone?: string;           // Optional -- may or may not exist
    department: string;
    salary: number;
}

let employee: Employee = {
    id: 1,
    name: "Alice",
    email: "alice@company.com",
    department: "Engineering",
    salary: 75000
};

// employee.id = 2; // Error: Cannot assign to 'id' because it is a read-only property
employee.name = "Alicia"; // OK -- not readonly
employee.phone = "555-0100"; // OK -- was optional, now added
```

### Example 2: Interface Methods

```typescript
interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
    multiply(a: number, b: number): number;
    divide(a: number, b: number): number;
}

const basicCalc: Calculator = {
    add: (a, b) => a + b,
    subtract: (a, b) => a - b,
    multiply: (a, b) => a * b,
    divide: (a, b) => b !== 0 ? a / b : Infinity
};

// Using the interface
console.log(basicCalc.add(10, 5));       // 15
console.log(basicCalc.divide(10, 0));    // Infinity

// Method signature with arrow function syntax
interface Logger {
    error: (message: string, ...args: unknown[]) => void;
    warn: (message: string, ...args: unknown[]) => void;
    info: (message: string, ...args: unknown[]) => void;
}

const consoleLogger: Logger = {
    error: (msg, ...args) => console.error(`[ERROR] ${msg}`, ...args),
    warn: (msg, ...args) => console.warn(`[WARN] ${msg}`, ...args),
    info: (msg, ...args) => console.info(`[INFO] ${msg}`, ...args)
};
```

### Example 3: Interface for Function Types

```typescript
// Define a callable interface
interface StringFormatter {
    (input: string, uppercase: boolean): string;
}

let format: StringFormatter = (input, uppercase) => {
    return uppercase ? input.toUpperCase() : input.toLowerCase();
};

console.log(format("Hello", true));  // "HELLO"
console.log(format("Hello", false)); // "hello"

// Interface with constructor signature (newable)
interface PointConstructor {
    new (x: number, y: number): Point;
}

interface Point {
    x: number;
    y: number;
}

class Point2D implements Point {
    constructor(public x: number, public y: number) {}
}

function createPoint(cls: PointConstructor, x: number, y: number): Point {
    return new cls(x, y);
}

const point = createPoint(Point2D, 10, 20);
```

### Example 4: Interface for Indexable Types

```typescript
// String index signature
interface StringDictionary {
    [key: string]: string;
}

let translations: StringDictionary = {
    hello: "bonjour",
    goodbye: "au revoir",
    thankYou: "merci"
};

// Adding new entries is type-safe
translations["please"] = "s'il vous plait";
// translations["count"] = 42; // Error: number not assignable to string

// Number index signature (array-like)
interface NumberCollection {
    [index: number]: number;
    length: number;
}

let squares: NumberCollection = [1, 4, 9, 16, 25];
console.log(squares[2]); // 9
console.log(squares.length); // 5

// Mixed index signatures (both string and number)
interface MixedDictionary {
    [key: string]: string | number;
    [index: number]: string;
    name: string;
    version: number;
}

let config: MixedDictionary = {
    name: "app-config",
    version: 2,
    0: "default"
};
```

### Example 5: Interface with Generics

```typescript
// Generic interface
interface Repository<T> {
    getById(id: string): T | undefined;
    getAll(): T[];
    create(item: T): void;
    update(id: string, item: Partial<T>): void;
    delete(id: string): void;
}

interface User {
    id: string;
    name: string;
    email: string;
}

class UserRepository implements Repository<User> {
    private users: User[] = [];

    getById(id: string): User | undefined {
        return this.users.find(u => u.id === id);
    }

    getAll(): User[] {
        return [...this.users];
    }

    create(user: User): void {
        this.users.push(user);
    }

    update(id: string, updates: Partial<User>): void {
        const index = this.users.findIndex(u => u.id === id);
        if (index !== -1) {
            this.users[index] = { ...this.users[index], ...updates };
        }
    }

    delete(id: string): void {
        this.users = this.users.filter(u => u.id !== id);
    }
}

// Generic interface with constraints
interface Comparable<T> {
    compareTo(other: T): number;
}

class Product implements Comparable<Product> {
    constructor(public price: number) {}

    compareTo(other: Product): number {
        return this.price - other.price;
    }
}
```

### Example 6: Interface Extension

```typescript
interface Animal {
    name: string;
    makeSound(): string;
}

interface Dog extends Animal {
    breed: string;
    isTrained: boolean;
}

// Dog has: name, makeSound(), breed, isTrained
let myDog: Dog = {
    name: "Rex",
    breed: "Labrador",
    isTrained: true,
    makeSound: () => "Woof!"
};

// Multiple inheritance
interface DomesticAnimal {
    owner: string;
}

interface Pet extends Animal, DomesticAnimal {
    furColor: string;
}

// Pet has: name, makeSound(), owner, furColor
```

### Example 7: Declaration Merging

```typescript
// First declaration
interface User {
    name: string;
}

// Second declaration with the same name -- they merge
interface User {
    age: number;
}

// Third declaration
interface User {
    email: string;
}

// User now has all three properties: name, age, email
let user: User = {
    name: "Alice",
    age: 30,
    email: "alice@example.com"
};

// This is useful for extending types from different sources
// For example, adding properties to Express Request:
// declare namespace Express {
//     interface Request {
//         user?: User;
//     }
// }
```

### Example 8: Interface vs Type Alias Comparison

```typescript
// Interface
interface Point {
    x: number;
    y: number;
}

// Type alias (equivalent for objects)
type PointType = {
    x: number;
    y: number;
};

// Interface can be extended
interface Point3D extends Point {
    z: number;
}

// Type alias uses intersection for extension
type Point3DType = PointType & { z: number };

// Type alias can represent primitives
type ID = number;
type Status = "active" | "inactive";

// Interface cannot
// interface ID extends number {} // Error

// Type alias for union types
type Shape = "circle" | "square" | "triangle";

// Declaration merging: unique to interfaces
interface Window {
    title: string;
}
interface Window {
    width: number;
}
interface Window {
    height: number;
}
// Window now has title, width, height
```

### Example 9: Implementing Interfaces in Classes

```typescript
interface Flyable {
    fly(): string;
    land(): string;
}

interface Swimmable {
    swim(): string;
    dive(depth: number): string;
}

class Duck implements Flyable, Swimmable {
    fly(): string {
        return "Duck is flying";
    }

    land(): string {
        return "Duck is landing on water";
    }

    swim(): string {
        return "Duck is swimming";
    }

    dive(depth: number): string {
        return `Duck is diving to ${depth} meters`;
    }
}

// Interface with constructor
interface ClockInterface {
    tick(): void;
}

interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) {}
    tick(): void {
        console.log("beep beep");
    }
}

function createClock(
    ctor: ClockConstructor,
    hour: number,
    minute: number
): ClockInterface {
    return new ctor(hour, minute);
}
```

### Example 10: Advanced Interface Patterns

```typescript
// Interface inheritance with compatible types
interface Base {
    id: number;
    createdAt: Date;
}

interface Updatable {
    updatedAt: Date;
}

interface SoftDeletable {
    deletedAt: Date | null;
}

// Combined interface
interface Entity extends Base, Updatable, SoftDeletable {
    version: number;
}

// Generic constraint using interface
function saveEntity<T extends Entity>(entity: T): void {
    console.log(`Saving entity ${entity.id} version ${entity.version}`);
}

// Interface with conditional types
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
    timestamp: Date;
}

// Pick from interface
type UserFields = Pick<User, "name" | "email">;

// Omit from interface
type UserWithoutEmail = Omit<User, "email">;

// Partial interface
type PartialUser = Partial<User>;

// Required interface (all optional become required)
interface Config {
    host?: string;
    port?: number;
}
type StrictConfig = Required<Config>;
// { host: string; port: number; }
```

## Analogy

An interface is like a job description -- it lists the required skills (properties) and responsibilities (methods) without specifying the person who will do the job. A type alias is like a nickname -- it gives a short, memorable name to something that could otherwise be a long description. The interface describes what something has; the type alias describes what something is.

## Tricky Things to Remember

- Interfaces with the same name in the same scope automatically merge. Type aliases with the same name cause a compile error.
- Type aliases can represent primitives, unions, and tuples; interfaces cannot.
- Interfaces can be extended using `extends`; type aliases use intersection (`&`).
- Type aliases can use computed properties (like `[key in ...]`), interfaces cannot.
- When defining object shapes, interfaces are generally preferred because of their merging and extending capabilities.

## More Tricky Scenarios

### Tricky 1: Interface Merging vs Type Alias Overlap

```typescript
interface Box {
    width: number;
}
interface Box {
    height: number;
}
interface Box {
    depth: number;
}
// Box has width, height, depth -- all merged

// Type alias -- this is an error:
// type BoxType = { width: number; };
// type BoxType = { height: number; }; // Error: Duplicate identifier
```

### Tricky 2: Readonly Array vs Readonly in Interface

```typescript
interface ImmutablePoint {
    readonly x: number;
    readonly y: number;
}

// Readonly applies to the properties, not the variable
let point: ImmutablePoint = { x: 10, y: 20 };
// point.x = 5; // Error
// But point itself can be reassigned:
point = { x: 1, y: 2 }; // OK -- the variable is not const
```

### Tricky 3: Index Signature with Specific Properties

```typescript
interface Dictionary {
    [key: string]: string;     // Index signature
    length: number;            // Error: type 'number' not assignable to 'string'
}

// Fix: make the index signature a union
interface DictionaryFixed {
    [key: string]: string | number;
    length: number;            // OK -- number is in the union
}
```

## Interview Questions with Answers

### 1. What is an interface in TypeScript and what is it used for?

An interface defines a contract or shape for objects, specifying what properties and methods they must have along with their types. Interfaces are used for type-checking object structures, defining contracts between components, providing documentation through types, enabling IDE autocompletion, and serving as reusable type definitions. They can be extended, merged, and implemented by classes.

### 2. What is a type alias and how does it differ from an interface?

A type alias creates a name for any type (primitives, unions, tuples, objects). The key differences: interfaces support declaration merging (same name merges), type aliases do not; type aliases can represent primitives, unions, and tuples, interfaces cannot; interfaces use `extends` for inheritance, type aliases use `&` (intersection); type aliases support computed and mapped types; interfaces are generally preferred for object shapes due to their merging capability.

### 3. Can type aliases represent primitive types? Can interfaces?

Type aliases can represent primitive types: `type ID = number; type Status = "active" | "inactive"; type NullableString = string | null;`. Interfaces cannot represent primitive types directly -- they can only describe object shapes. An interface must define properties or methods; it cannot create an alias for `number` or `string`. This is one of the main reasons to use type aliases.

### 4. What is declaration merging in interfaces?

Declaration merging is a feature unique to interfaces where multiple declarations of the same interface name in the same scope are automatically merged into a single interface with all properties combined. For example, `interface User { name: string; }` and `interface User { age: number; }` merge to create `User { name: string; age: number; }`. This is useful for augmenting types from external libraries or adding properties to global types.

### 5. How do you make a property optional in an interface?

Add a `?` after the property name: `interface User { name: string; email?: string; }`. The `email` property is optional and can be omitted when creating objects conforming to the interface. Optional properties have the type `Type | undefined` internally. They are useful for configuration objects, partial updates, and scenarios where some properties may not be present.

### 6. How do you define a function type using an interface?

Use a call signature: `interface MathOp { (a: number, b: number): number; }`. This defines an interface that describes a function type. Variables can then be typed with this interface: `let add: MathOp = (x, y) => x + y;`. You can also combine call signatures with properties for more complex patterns (a callable object with additional properties).

### 7. When would you choose a type alias over an interface?

Choose type aliases when: representing primitive types (`type ID = number`), creating union types (`type Status = "active" | "inactive"`), using tuple types (`type Pair = [string, number]`), working with mapped types (`type Readonly<T> = { readonly [K in keyof T]: T[K] }`), combining types with intersection (`type C = A & B`), or when declaration merging is not desired. For object shapes that may be extended, interfaces are generally preferred.

### 8. How do you extend an interface and how do you create an intersection of types?

Extend an interface using the `extends` keyword: `interface Admin extends User { role: string; }`. For multiple parents: `interface Employee extends User, Timestamped { ... }`. Create intersection types using `&`: `type Admin = User & { role: string; }`. For multiple: `type C = A & B & { ... }`. The main difference is that `extends` works only with interfaces, while `&` works with any types including type aliases.

### 9. Can a type alias be extended? How do you achieve similar functionality?

Type aliases cannot be extended using `extends`, but you can achieve the same effect using intersection types: `type Admin = User & { role: string; permissions: string[]; };`. This creates a new type that has all properties of `User` plus the additional properties. You can also use intersections to combine multiple types: `type C = A & B & { customProp: number; };`.

### 10. What are readonly properties in an interface?

Readonly properties (marked with `readonly` keyword) can only be assigned when the object is first created and cannot be modified afterward. For example: `interface Point { readonly x: number; readonly y: number; }`. Attempting to modify a readonly property causes a compile error: `point.x = 5;` // Error. Readonly is a compile-time check only -- it does not enforce immutability at runtime.
