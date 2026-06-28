# Fundamentals of Type Aliases

## Overview

A type alias creates a new name for an existing type. It does not create a new type; it is simply a synonym or shorthand for a type definition. Type aliases are incredibly powerful for simplifying complex type expressions and making code more readable.

## Basic Type Aliases

```typescript
// Alias for a primitive type
type ID = number;
let userId: ID = 12345;

// This is equivalent to:
let userId2: number = 12345;
```

While aliasing primitives may seem useless initially, it becomes valuable when the semantics matter:

```typescript
type UserID = number;
type ProductID = number;
type OrderID = number;

function getUser(id: UserID): void { /* ... */ }
function getProduct(id: ProductID): void { /* ... */ }
function getOrder(id: OrderID): void { /* ... */ }
```

This documents the intent of each parameter even though they are all numbers under the hood.

## Union Types with Type Aliases

The most common and useful application of type aliases:

```typescript
// Without alias -- verbose and repetitive
function processStatus(status: "active" | "inactive" | "pending" | "suspended"): void {
    // ...
}

// With alias -- clean and reusable
type AccountStatus = "active" | "inactive" | "pending" | "suspended";

function processStatus(status: AccountStatus): void {
    // ...
}

function filterByStatus(status: AccountStatus): void {
    // ...
}

// Union of other types
type NullableString = string | null;
type NumericOrString = number | string;
type Result = { success: true; data: unknown } | { success: false; error: string };
```

## Object Types with Type Aliases

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    role: "admin" | "editor" | "viewer";
};

type Product = {
    id: number;
    name: string;
    price: number;
    inStock: boolean;
};

function createUser(user: User): void {
    console.log(`User: ${user.name}, Role: ${user.role}`);
}

function updateProduct(product: Product): void {
    console.log(`Product: ${product.name}, Price: $${product.price}`);
}
```

## Function Type Aliases

```typescript
// Type alias for a function signature
type Comparator<T> = (a: T, b: T) => number;
type EventHandler = (event: Event) => void;
type AsyncCallback<T> = (error: Error | null, result?: T) => void;

// Usage
const numberComparator: Comparator<number> = (a, b) => a - b;
const stringComparator: Comparator<string> = (a, b) => a.localeCompare(b);

function sortArray<T>(arr: T[], comparator: Comparator<T>): T[] {
    return [...arr].sort(comparator);
}
```

## Generic Type Aliases

```typescript
// Generic type alias
type ApiResponse<T> = {
    success: boolean;
    data: T;
    message?: string;
};

type PaginatedResult<T> = {
    items: T[];
    total: number;
    page: number;
    pageSize: number;
};

// Usage
type UserResponse = ApiResponse<User>;
type PaginatedUsers = PaginatedResult<User>;

let response: UserResponse = {
    success: true,
    data: { id: 1, name: "Alice", email: "alice@example.com", role: "admin" }
};
```

## Type Aliases for Complex Types

```typescript
// Nested and complex type
type DeepPartial<T> = {
    [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

// Mapped type with conditional
type Nullable<T> = {
    [K in keyof T]: T[K] | null;
};

// Template literal type
type EventName = `on${Capitalize<string>}`;
type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type APIEndpoint = `/${string}`;
```

## Combining Type Aliases with Intersection

```typescript
type WithTimestamp = {
    createdAt: Date;
    updatedAt: Date;
};

type WithMetadata = {
    version: number;
    source: string;
};

// Intersection combines multiple types
type AuditedEntity = WithTimestamp & WithMetadata & {
    id: number;
};

let entity: AuditedEntity = {
    createdAt: new Date(),
    updatedAt: new Date(),
    version: 1,
    source: "system",
    id: 100
};
```

## Type Aliases vs Interfaces Summary

| Aspect              | Type Alias                              | Interface                             |
|---------------------|-----------------------------------------|---------------------------------------|
| Primitives          | Yes (`type ID = number`)                | No                                    |
| Union types         | Yes (`type Status = "on" \| "off"`)    | No                                    |
| Intersection        | Using `&`                               | Using `extends`                       |
| Declaration merging | Not supported                           | Supported                             |
| Extends             | Via intersection (`&`)                  | Via `extends` keyword                 |
| Computed/mapped     | Yes                                     | No                                    |

## Analogy

A type alias is like giving a nickname to a phone number. Instead of saying "call 555-1234," you say "call Home." The nickname does not change the number itself; it just provides a convenient, memorable name for it. Similarly, `type ID = number` does not create a new type -- it creates a more descriptive name for the existing `number` type.

## Tricky Things to Remember

- A type alias is NOT a new type -- it is just an alias. `type ID = number` means `ID` and `number` are interchangeable.
- You cannot use a type alias in `implements` or `extends` clauses the same way you use an interface. However, you can use intersection types to simulate extension.
- Type aliases can reference themselves recursively (e.g., for tree structures), but must be careful to avoid infinite recursion.
- Type aliases can use computed property names and template literal types, which interfaces cannot.
- Type aliases cannot be re-opened or merged -- redeclaring the same type alias name causes an error.

## Interview Questions

1. What is a type alias and how do you create one?
2. Can a type alias represent a union type? Give an example.
3. How do you create a generic type alias?
4. What is the difference between a type alias and an interface?
5. Can a type alias extend another type? How?
6. Why would you alias a primitive type like `type ID = number`?
7. How do you use a type alias to define a function signature?
8. Can type aliases be recursive?
9. What is declaration merging and why does it not work with type aliases?
10. How do you use type aliases with mapped and conditional types?
