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

## Intersection vs Union

```typescript
// Union: value can be EITHER type
type Union = string | number;
let u: Union = "hello"; // OK
u = 42;                  // OK

// Intersection: value must satisfy ALL types
// string & number would be impossible (a value cannot be both a string and a number)
```

## Intersection with Objects

The most common use of intersection types is combining object shapes:

```typescript
type User = {
    name: string;
    email: string;
};

type Admin = User & {
    role: "admin";
    permissions: string[];
};

// Admin now requires:
// - name: string      (from User)
// - email: string     (from User)
// - role: "admin"     (from Admin)
// - permissions: string[] (from Admin)

let admin: Admin = {
    name: "Alice",
    email: "alice@example.com",
    role: "admin",
    permissions: ["read", "write", "delete"]
};
```

This is functionally similar to interface extension:

```typescript
// Using interface extension
interface AdminInterface extends User {
    role: "admin";
    permissions: string[];
}

// Using intersection type
type AdminType = User & {
    role: "admin";
    permissions: string[];
};
```

## Intersection with Multiple Types

```typescript
type WithID = { id: number };
type WithTimestamp = { createdAt: Date; updatedAt: Date };
type WithMetadata = { version: number; author: string };

// Combine all three
type Document = WithID & WithTimestamp & WithMetadata & {
    title: string;
    content: string;
};

let doc: Document = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    version: 1,
    author: "Alice",
    title: "TypeScript Guide",
    content: "Intersection types are powerful..."
};
```

## Intersection with Method Types

```typescript
type Loggable = {
    log(message: string): void;
};

type Serializable = {
    toJSON(): string;
};

type LoggerService = Loggable & Serializable;

let logger: LoggerService = {
    log(message: string): void {
        console.log(`[LOG]: ${message}`);
    },
    toJSON(): string {
        return JSON.stringify({ type: "logger" });
    }
};
```

## Intersection with Union Members

When intersecting types that have overlapping properties with different types, the intersection takes the combined requirements:

```typescript
type A = { value: string | number };
type B = { value: number };

// C = { value: number }  -- because number is the intersection of string|number and number
type C = A & B;

let c: C = { value: 42 };       // OK
// let c2: C = { value: "hello" }; // Error: Type 'string' is not assignable to type 'number'
```

## Intersection of Non-Object Types (Primitives)

Intersecting primitives often results in `never` because a value cannot be both a string and a number simultaneously:

```typescript
type Impossible = string & number;
// This is effectively 'never' -- no value can be both a string and a number

// More practical example:
type TruthyString = string & { length: number };
// This is still just 'string' in practice, since all strings have a length property
```

## Intersection Types with Interfaces

```typescript
interface Nameable {
    name: string;
}

interface Ageable {
    age: number;
}

// Intersection of interfaces
type Person = Nameable & Ageable;

let person: Person = {
    name: "Alice",
    age: 30
};
```

## Practical Example

```typescript
type Identifiable = { id: number };
type Timestamped = { createdAt: Date; updatedAt: Date };
type SoftDeletable = { deletedAt: Date | null; deletedBy: string | null };

// A complete entity type using intersections
type BaseEntity = Identifiable & Timestamped & SoftDeletable;

type Product = BaseEntity & {
    name: string;
    price: number;
    category: string;
    inStock: boolean;
};

type Order = BaseEntity & {
    productId: number;
    quantity: number;
    total: number;
    status: "pending" | "shipped" | "delivered" | "cancelled";
};

// Usage
const product: Product = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    deletedAt: null,
    deletedBy: null,
    name: "Wireless Mouse",
    price: 29.99,
    category: "Electronics",
    inStock: true
};
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

## Interview Questions

1. What is an intersection type in TypeScript and how is it created?
2. What is the difference between an intersection type (`&`) and a union type (`|`)?
3. How do intersection types compare to interface extension?
4. What happens when you intersect two types that have the same property with incompatible types?
5. Can you intersect primitive types like `string & number`? What is the result?
6. How would you model a "mixin" pattern using intersection types?
7. What is the difference between `type C = A & B` and `interface C extends A, B`?
8. How do intersection types work with generic types?
9. Can an intersection type be used in a class `implements` clause?
10. What is the result of intersecting a union type with another type?
