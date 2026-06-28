# Intersection Types

## Overview

Intersection types allow you to combine multiple types into one. A value of an intersection type must satisfy ALL constituent types simultaneously. Intersection types are created using the `&` operator.

## Basic Syntax

```typescript
type A = { a: string };
type B = { b: number };

// Intersection: must have BOTH properties
type C = A & B;

let value: C = {
    a: "hello",
    b: 42
};
```

## Expanded Code Examples

### Example 1: Intersection vs Union

```typescript
// Union: value can be EITHER type
type Union = string | number;
let u: Union = "hello"; // OK
u = 42;                  // OK

// Intersection: value must satisfy ALL types
// string & number would be impossible (a value cannot be both a string and a number)

// Practical difference with objects:
type HasName = { name: string };
type HasAge = { age: number };

// Union: either has name OR age (or both)
type UnionPerson = HasName | HasAge;
let p1: UnionPerson = { name: "Alice" };    // OK -- just name
let p2: UnionPerson = { age: 30 };          // OK -- just age
let p3: UnionPerson = { name: "Bob", age: 25 }; // OK -- both

// Intersection: must have BOTH name AND age
type IntersectionPerson = HasName & HasAge;
let p4: IntersectionPerson = { name: "Charlie", age: 35 }; // OK -- both
// let p5: IntersectionPerson = { name: "Diana" };  // Error: missing age
```

### Example 2: Mixin Pattern with Intersection Types

```typescript
// Reusable "mixin" types
type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type SoftDeletable = {
    deletedAt: Date | null;
    deletedBy: string | null;
};

type Auditable = {
    createdBy: string;
    lastModifiedBy: string;
    version: number;
};

type Identifiable = {
    id: number;
};

// Combine mixins to create entity types
type BaseEntity = Identifiable & Timestamped & SoftDeletable & Auditable;

type User = BaseEntity & {
    name: string;
    email: string;
    role: "admin" | "user" | "viewer";
};

type Product = BaseEntity & {
    name: string;
    price: number;
    category: string;
    inStock: boolean;
    sku: string;
};

// Create a user
const user: User = {
    id: 1,
    createdAt: new Date("2024-01-15"),
    updatedAt: new Date("2024-06-20"),
    deletedAt: null,
    deletedBy: null,
    createdBy: "system",
    lastModifiedBy: "admin",
    version: 3,
    name: "Alice",
    email: "alice@example.com",
    role: "admin"
};

// Create a product
const product: Product = {
    id: 100,
    createdAt: new Date(),
    updatedAt: new Date(),
    deletedAt: null,
    deletedBy: null,
    createdBy: "admin",
    lastModifiedBy: "admin",
    version: 1,
    name: "Wireless Mouse",
    price: 29.99,
    category: "Electronics",
    inStock: true,
    sku: "WM-001"
};
```

### Example 3: Intersection with Overlapping Properties

```typescript
// When intersected types have the same property name,
// the property types are intersected as well
type A = { value: string | number };
type B = { value: number | boolean };

// C = { value: number } -- because number is the intersection of (string|number) & (number|boolean)
type C = A & B;

let c1: C = { value: 42 };    // OK -- number is in both
// let c2: C = { value: "hello" }; // Error: string is not in number|boolean
// let c3: C = { value: true };    // Error: boolean is not in string|number

// With non-overlapping types, properties are combined
type X = { a: string; b: number };
type Y = { a: number; c: boolean };

// Z = { a: never; b: number; c: boolean } -- 'a' is never because string & number = never
type Z = X & Y;

// let z: Z = { a: "hello", b: 1, c: true }; // Error: 'a' is never
// let z: Z = { a: 42, b: 1, c: true };      // Error: 'a' is never
// 'a' cannot be assigned because string & number produces never
```

### Example 4: Intersection with Function Types

```typescript
type Loggable = {
    log(message: string): void;
};

type Serializable = {
    toJSON(): string;
};

type Startable = {
    start(): void;
    stop(): void;
};

// A service that combines multiple capabilities
type LoggerService = Loggable & Serializable & Startable;

class AppLogger implements LoggerService {
    private logs: string[] = [];

    log(message: string): void {
        this.logs.push(message);
        console.log(`[LOG]: ${message}`);
    }

    toJSON(): string {
        return JSON.stringify({ logs: this.logs });
    }

    start(): void {
        console.log("Logger started");
    }

    stop(): void {
        console.log("Logger stopped");
    }
}

// Intersection with overloaded function types
type Fn1 = (x: string) => number;
type Fn2 = (x: number) => string;

// Intersection of functions creates an overloaded function type
type Overloaded = Fn1 & Fn2;

const fn: Overloaded = (x: string | number): string | number => {
    if (typeof x === "string") {
        return x.length; // number
    }
    return String(x);   // string
};
```

### Example 5: Intersection with Generic Types

```typescript
// Generic intersection utility
type WithId<T> = T & { id: number };
type WithDates<T> = T & { createdAt: Date; updatedAt: Date };
type WithAudit<T> = T & { createdBy: string; lastModifiedBy: string };

// Compose generics
type FullEntity<T> = WithId<WithDates<WithAudit<T>>>;

interface UserData {
    name: string;
    email: string;
}

type User = FullEntity<UserData>;
// Result: UserData & { id: number } & { createdAt: Date; updatedAt: Date } & { createdBy: string; lastModifiedBy: string }

const user: User = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    createdBy: "system",
    lastModifiedBy: "system",
    name: "Alice",
    email: "alice@test.com"
};

// Generic intersection with conditional types
type Merge<T, U> = {
    [K in keyof T | keyof U]: K extends keyof T
        ? K extends keyof U ? T[K] | U[K] : T[K]
        : K extends keyof U ? U[K] : never;
};

interface Person {
    name: string;
    age: number;
}

interface Contact {
    email: string;
    phone: string;
}

type Employee = Merge<Person, Contact>;
// { name: string; age: number; email: string; phone: string; }
```

### Example 6: Intersection with Union and Discriminated Unions

```typescript
// Intersection with union members
type SuccessState = { status: "success"; data: unknown };
type ErrorState = { status: "error"; error: string };
type LoadingState = { status: "loading" };

type State = SuccessState | ErrorState | LoadingState;

// Intersect with common properties
type TimestampedState = State & { timestamp: Date };
// Each union member now also has timestamp
// Equivalent to:
// { status: "success"; data: unknown; timestamp: Date }
// | { status: "error"; error: string; timestamp: Date }
// | { status: "loading"; timestamp: Date }

function logState(state: TimestampedState): void {
    console.log(`[${state.timestamp.toISOString()}] State: ${state.status}`);
    // The discriminated union still works
    if (state.status === "success") {
        console.log("Data:", state.data);
    }
}

// Intersection with array types
type NumericArray = number[];
type StringArray = string[];

// Intersection of arrays is never (a value cannot be both number[] and string[])
type Impossible = NumericArray & StringArray;

// But intersection of array of objects works
type NamedArray = { name: string }[];
type AgedArray = { age: number }[];
type PersonArray = NamedArray & AgedArray;
// This is ({ name: string } & { age: number })[] = { name: string; age: number }[]
```

### Example 7: Intersection with Primitive and Literal Types

```typescript
// Intersection of primitives often produces never
type Impossible = string & number;    // never -- no value can be both

// But some intersections are meaningful
type TruthyString = string & { length: number };
// All strings have a length property, so this is just 'string'

type SpecificNumber = number & (1 | 2 | 3);
// This is 1 | 2 | 3 -- the intersection of number with 1|2|3

// Intersection with literal types
type AdminRole = { role: "admin" };
type UserRole = { role: "user" };

// Intersection of incompatible literal types produces never for the property
type ConflictingRole = AdminRole & UserRole;
// { role: never } -- role cannot be both "admin" and "user"

// However, this can be useful:
type WithRole = { role: string };
type Admin = { role: "admin" };
type AdminWithExtra = Admin & { permissions: string[] };
// Result: { role: "admin"; permissions: string[]; }
```

### Example 8: Intersection in Class Implementations

```typescript
interface Flyable {
    fly(distance: number): string;
}

interface Swimmable {
    swim(distance: number): string;
}

interface Runnable {
    run(distance: number): string;
}

// A class can implement the intersection of multiple interfaces
class AmphibiousVehicle implements Flyable, Swimmable, Runnable {
    constructor(private name: string) {}

    fly(distance: number): string {
        return `${this.name} flew ${distance}km`;
    }

    swim(distance: number): string {
        return `${this.name} swam ${distance}km`;
    }

    run(distance: number): string {
        return `${this.name} ran ${distance}km`;
    }
}

// Function that accepts the intersection type
function testVehicle(vehicle: Flyable & Swimmable & Runnable): void {
    console.log(vehicle.fly(100));
    console.log(vehicle.swim(50));
    console.log(vehicle.run(20));
}

const hovercraft = new AmphibiousVehicle("HoverCraft-1");
testVehicle(hovercraft);

// TypeScript's structural typing means you don't need a named interface:
function testVehicleInline(vehicle: {
    fly(distance: number): string;
    swim(distance: number): string;
    run(distance: number): string;
}): void {
    // Same as above but inline
}
```

### Example 9: Intersection with Type Guards

```typescript
interface Admin {
    role: "admin";
    permissions: string[];
    accessLevel: number;
}

interface User {
    role: "user";
    department: string;
    manager: string;
}

type Person = Admin & User; // This would produce 'role: never'

// Instead, use union for disjoint types:
type PersonUnion = Admin | User;

// But intersection is useful when types are complementary:
interface HasEmail {
    email: string;
}

interface HasPhone {
    phone: string;
}

type Contactable = HasEmail & HasPhone;

function contactPerson(person: Contactable): void {
    console.log(`Email: ${person.email}, Phone: ${person.phone}`);
}

// Custom type guard for intersection type
function isContactable(value: unknown): value is Contactable {
    return (
        typeof value === "object" &&
        value !== null &&
        "email" in value &&
        "phone" in value
    );
}

function safeContact(value: unknown): void {
    if (isContactable(value)) {
        // value is narrowed to Contactable (HasEmail & HasPhone)
        contactPerson(value);
    }
}
```

### Example 10: Advanced Intersection Compositions

```typescript
// Deep intersection -- combining nested structures
type ConfigA = {
    server: {
        host: string;
        port: number;
    };
    logging: {
        level: string;
    };
};

type ConfigB = {
    server: {
        protocol: "http" | "https";
    };
    database: {
        url: string;
    };
};

// Simple intersection only merges top-level properties
type MergedConfig = ConfigA & ConfigB;
// { server: { host: string; port: number; } & { protocol: "http" | "https" };
//   logging: { level: string; };
//   database: { url: string; }; }

const config: MergedConfig = {
    server: {
        host: "localhost",
        port: 8080,
        protocol: "http"
    },
    logging: {
        level: "debug"
    },
    database: {
        url: "mongodb://localhost/db"
    }
};

// Intersection with function overloads
type SimpleHandler = (event: string) => void;
type DetailedHandler = (event: string, data: Record<string, unknown>) => void;

type Handler = SimpleHandler & DetailedHandler;

const handler: Handler = (event: string, data?: Record<string, unknown>) => {
    if (data) {
        console.log(`Event: ${event}`, data);
    } else {
        console.log(`Event: ${event}`);
    }
};

handler("click");                     // OK -- matches SimpleHandler
handler("click", { x: 10, y: 20 });  // OK -- matches DetailedHandler

// Intersection with map/record types
type StringMap = Record<string, string>;
type NumberMap = Record<string, number>;

// Combined map that can have both string and number values
type CombinedMap = StringMap & NumberMap;
// { [key: string]: string & number } = { [key: string]: never }
// This is because string & number = never

// Instead, use a union map:
type ValueMap = Record<string, string | number>;
```

## Intersection vs Interface Extension

| Feature                | Interface `extends`              | Intersection `&`                  |
|------------------------|----------------------------------|-----------------------------------|
| Syntax                 | `interface B extends A {}`       | `type B = A & { ... }`            |
| Multiple parents       | `interface C extends A, B {}`    | `type C = A & B & { ... }`        |
| Declaration merging    | Yes (for interfaces)             | No (types cannot merge)           |
| Conflicting properties | Compile-time error (incompatible types) | Produces `never` for the conflicting property |
| Usable in `implements` | Yes                              | No (but intersection of interfaces works) |

## Analogy

An intersection type is like a multi-tool that combines a knife, scissors, and a screwdriver into a single device. You get all the capabilities in one package. If `User` gives you a name and email, and `Admin` gives you role and permissions, then `User & Admin` gives you all four. It is like ordering a "combo meal" at a restaurant -- you get the burger AND the fries AND the drink, not one or the other.

## Tricky Things to Remember

- Intersection types with conflicting primitive types (e.g., `string & number`) result in `never`.
- Intersection types are different from interface extension -- they are more flexible but do not support declaration merging.
- When two intersected types have the same property with incompatible types, the property type becomes `never`, making the whole intersection potentially unusable.
- You can use intersection types to "mix in" reusable type fragments (like `WithTimestamp`, `Identifiable`).
- Intersection types work with any types, including unions, primitives, and generics, making them extremely versatile.

## More Tricky Scenarios

### Tricky 1: Intersection of Unions vs Union of Intersections

```typescript
type A = { a: string } | { b: number };
type B = { c: boolean } | { d: Date };

// Intersection of unions:
type IntersectionOfUnions = A & B;
// ({ a: string } & { c: boolean }) | ({ a: string } & { d: Date }) |
// ({ b: number } & { c: boolean }) | ({ b: number } & { d: Date })

// This creates 4 possible combinations -- can be complex
```

### Tricky 2: Intersection with never

```typescript
type Impossible = string & number; // never

// Intersection with never propagates:
type AlsoImpossible = Impossible & { anything: string };
// never & { anything: string } = never

// This can be used intentionally to filter types:
type Filter<T> = T extends string ? T & { __brand: "string" } : never;
// Filter<string | number> = (string & { __brand: "string" }) | never = string & { __brand: "string" }
```

### Tricky 3: Intersection Type Inference

```typescript
function combine<T, U>(first: T, second: U): T & U {
    return { ...first, ...second };
}

const result = combine(
    { name: "Alice", age: 30 },
    { email: "alice@test.com", role: "admin" }
);

// Inferred type: { name: string; age: number; } & { email: string; role: string; }
// Equivalent to: { name: string; age: number; email: string; role: string; }
```

## Interview Questions with Answers

### 1. What is an intersection type in TypeScript and how is it created?

An intersection type combines multiple types into one using the `&` operator. A value of an intersection type must satisfy ALL constituent types simultaneously. For example, `type C = A & B` means `C` has all properties from both `A` and `B`. Intersection types are the opposite of union types: union means "this OR that," intersection means "this AND that."

### 2. What is the difference between an intersection type (`&`) and a union type (`|`)?

Union types (`|`) mean a value can be one of several types (EITHER `A` OR `B`). Intersection types (`&`) mean a value must satisfy all types at once (BOTH `A` AND `B`). For objects: `A | B` has properties from `A` OR `B` (or both). `A & B` must have all properties from both `A` AND `B`. Union types model choice/alternatives; intersection types model combination/merging.

### 3. How do intersection types compare to interface extension?

Both combine object shapes. Interface `extends` creates a new named interface with clear inheritance: `interface B extends A { ... }`. Intersection `&` combines any types: `type B = A & { ... }`. Key differences: `extends` supports declaration merging; `&` does not. Conflicting properties cause compile errors with `extends`, but produce `never` with `&`. Interfaces can be `implemented` by classes; intersections cannot be used directly in `implements` clauses.

### 4. What happens when you intersect two types that have the same property with incompatible types?

The property type becomes `never`, which effectively makes the entire intersection unusable. For example, if `A` has `value: string` and `B` has `value: number`, then `A & B` has `value: string & number` which is `never`. No value can satisfy `never`, so you cannot create an object that satisfies both. TypeScript will not warn about this at the type definition, only when you try to assign a value.

### 5. Can you intersect primitive types like `string & number`? What is the result?

Yes, you can, but the result is `never` because no value can be both a `string` and a `number` simultaneously. Intersection of primitives is generally not useful except for certain edge cases like `string & { length: number }` (which simplifies to `string` since all strings have a length property). Most primitive intersections result in `never` and indicate a logical impossibility.

### 6. How would you model a "mixin" pattern using intersection types?

Create small, focused type fragments and combine them with `&`: `type Timestamped = { createdAt: Date; updatedAt: Date; }; type SoftDeletable = { deletedAt: Date | null; }; type Entity = Timestamped & SoftDeletable & { id: number; };`. This promotes code reuse, modularity, and composition over inheritance. Each fragment represents a cross-cutting concern that can be mixed into multiple entity types.

### 7. What is the difference between `type C = A & B` and `interface C extends A, B`?

`type C = A & B` uses intersection types and works with any type (including unions, primitives, and generics). `interface C extends A, B` only works with object types and interfaces. Intersection types with conflicting properties produce `never` silently; interface extension produces a compile-time error. Interfaces support declaration merging; intersection types do not. Interfaces can be `implemented` by classes; intersection types cannot.

### 8. How do intersection types work with generic types?

Intersection types work seamlessly with generics. You can create generic intersection utilities: `type WithId<T> = T & { id: number; }; type WithDates<T> = T & { createdAt: Date; updatedAt: Date; }; type FullEntity<T> = WithId<WithDates<WithAudit<T>>>;`. Generics allow the base type to vary while the intersected properties remain fixed. This is powerful for creating flexible, composable type systems.

### 9. Can an intersection type be used in a class `implements` clause?

No, you cannot directly use `class Foo implements A & B { ... }`. However, you can achieve the same effect by implementing each interface separately: `class Foo implements A, B { ... }`. If `A` and `B` are type aliases (not interfaces), you cannot use them in `implements` at all. In that case, define an interface that extends both types or use intersection through structural typing.

### 10. What is the result of intersecting a union type with another type?

Intersecting a union with another type distributes the intersection across each member of the union. For example, `(A | B) & C` is equivalent to `(A & C) | (B & C)`. This is called distributivity. It means the resulting type is a union where each member of the original union is combined with the intersecting type. This can create complex nested types that represent all possible valid combinations.
