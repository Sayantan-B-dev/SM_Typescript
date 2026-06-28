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

### Optional Properties in Interfaces

Use the `?` modifier to mark a property as optional:

```typescript
interface Config {
    host: string;
    port?: number;     // Optional
    debug?: boolean;   // Optional
}

function setupServer(config: Config): void {
    console.log(`Host: ${config.host}`);
    if (config.port) {
        console.log(`Port: ${config.port}`);
    }
}

// Both are valid:
setupServer({ host: "localhost" });
setupServer({ host: "localhost", port: 8080, debug: true });
```

### Readonly Properties in Interfaces

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}

let p: Point = { x: 10, y: 20 };
// p.x = 5; // Error: Cannot assign to 'x' because it is a read-only property
```

### Interface Method Signatures

```typescript
interface Product {
    id: number;
    name: string;
    price: number;
    // Method signature
    getDiscountedPrice(discount: number): number;
    // Method signature using arrow function syntax
    displayInfo: () => string;
}

const item: Product = {
    id: 1,
    name: "Laptop",
    price: 1000,
    getDiscountedPrice(discount: number): number {
        return this.price - (this.price * discount) / 100;
    },
    displayInfo: () => `Product: ${item.name}`
};
```

### Interface for Function Types

```typescript
interface MathOperation {
    (a: number, b: number): number;
}

let add: MathOperation = (x, y) => x + y;
let multiply: MathOperation = (x, y) => x * y;
```

### Interface for Indexable Types

```typescript
interface StringDictionary {
    [key: string]: string;
}

let translations: StringDictionary = {
    hello: "bonjour",
    goodbye: "au revoir"
};

interface NumberArray {
    [index: number]: number;
}

let scores: NumberArray = [95, 87, 92];
```

## Type Aliases

A type alias creates a new name for any type, not just object shapes.

```typescript
// Type alias for a primitive
type ID = number;

// Type alias for a union
type Status = "active" | "inactive" | "pending";

// Type alias for an object shape
type User = {
    name: string;
    email: string;
    age: number;
};

// Type alias for a function signature
type Callback = (error: Error | null, data: unknown) => void;

// Type alias for complex nested types
type ApiResponse<T> = {
    success: boolean;
    data: T;
    error?: string;
};
```

## Interfaces vs Type Aliases

| Feature                 | Interface                             | Type Alias                           |
|-------------------------|---------------------------------------|--------------------------------------|
| Object shape            | Yes                                   | Yes                                  |
| Union types             | No                                    | Yes                                  |
| Intersection types      | Via extends                           | Using `&`                            |
| Declaration merging     | Yes (same name merges)                | No (duplicate name errors)           |
| Extends/Implements      | Yes                                   | No (but can use intersection)        |
| Primitive types         | No                                    | Yes (e.g., `type ID = number`)       |
| Computed properties     | No                                    | Yes                                  |
| Mapped types            | No                                    | Yes                                  |

### Declaration Merging (Interface Only)

```typescript
// First declaration
interface User {
    name: string;
}

// Second declaration with the same name -- they merge
interface User {
    age: number;
}

// User now has both 'name' and 'age'
let user: User = {
    name: "Alice",
    age: 30
};
```

## Practical Example: When to Use Interface vs Type

```typescript
// Use interface for object shapes that may be extended
interface Animal {
    name: string;
    sound(): string;
}

// Use type for unions, intersections, and computed types
type DogBreed = "Labrador" | "Poodle" | "Bulldog";
type CatBreed = "Siamese" | "Persian" | "Maine Coon";
type PetBreed = DogBreed | CatBreed;

// Interface can be extended
interface Dog extends Animal {
    breed: DogBreed;
}

// Type can use intersection
type Cat = Animal & {
    breed: CatBreed;
};
```

## Analogy

An interface is like a job description -- it lists the required skills (properties) and responsibilities (methods) without specifying the person who will do the job. A type alias is like a nickname -- it gives a short, memorable name to something that could otherwise be a long description. The interface describes what something has; the type alias describes what something is.

## Tricky Things to Remember

- Interfaces with the same name in the same scope automatically merge. Type aliases with the same name cause a compile error.
- Type aliases can represent primitives, unions, and tuples; interfaces cannot.
- Interfaces can be extended using `extends`; type aliases use intersection (`&`).
- Type aliases can use computed properties (like `[key in ...]`), interfaces cannot.
- When defining object shapes, interfaces are generally preferred because of their merging and extending capabilities.

## Interview Questions

1. What is an interface in TypeScript and what is it used for?
2. What is a type alias and how does it differ from an interface?
3. Can type aliases represent primitive types? Can interfaces?
4. What is declaration merging in interfaces?
5. How do you make a property optional in an interface?
6. How do you define a function type using an interface?
7. When would you choose a type alias over an interface?
8. How do you extend an interface and how do you create an intersection of types?
9. Can a type alias be extended? How do you achieve similar functionality?
10. What are readonly properties in an interface?
