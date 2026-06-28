# Any, Unknown, Void, and More

## Overview

TypeScript provides special types for situations where the type is uncertain, absent, or intentionally flexible.

## The `any` Type

When a value is typed as `any`, TypeScript disables all type checking for that value.

```typescript
let value: any = 42;
value = "hello";      // OK
value = true;         // OK
value = { key: "val" }; // OK

// No type checking at all
value.toUpperCase();  // Runtime error, but no compile-time error
value.nonexistent.property; // No compile-time error
```

**When to use `any`:**
- Migrating JavaScript to TypeScript gradually.
- Working with third-party libraries without type definitions.
- Prototyping quickly.

**Why to avoid `any`:**
- It defeats the purpose of TypeScript's type safety.
- Type errors that would be caught at compile time become runtime errors.
- It makes code harder to understand and maintain.

## The `unknown` Type

`unknown` is the type-safe counterpart of `any`. You cannot perform operations on an `unknown` value without narrowing its type first.

```typescript
let value: unknown = 42;

// These all cause compile-time errors:
// value.toUpperCase();     // Error: Object is of type 'unknown'
// let str: string = value; // Error: Type 'unknown' is not assignable to type 'string'

// You must narrow the type first
if (typeof value === "string") {
    // Inside this block, TypeScript knows value is a string
    console.log(value.toUpperCase());
}

if (typeof value === "number") {
    console.log(value + 10); // Works
}
```

### Key Difference: `any` vs `unknown`

| Feature               | `any`                               | `unknown`                           |
|-----------------------|--------------------------------------|--------------------------------------|
| Type checking         | Disabled                             | Enforced                             |
| Can assign to others  | Yes (to any type)                    | No (must narrow first)               |
| Can perform operations| Yes (any operation allowed)          | No (must narrow first)               |
| Safety                | Unsafe                               | Safe                                 |

## The `void` Type

`void` represents the absence of a return value, typically used for functions that do not return anything.

```typescript
// Function that returns nothing
function logMessage(message: string): void {
    console.log(message);
    // No return statement
}

// void vs undefined
function returnsUndefined(): undefined {
    return undefined;
}

// A void function can return undefined implicitly
function implicitVoid(): void {
    // No return -- valid
}

function explicitVoid(): void {
    return; // valid -- returns undefined
}

// But returning a value is an error:
// function invalid(): void {
//     return 42; // Error: Type 'number' is not assignable to type 'void'
// }
```

## The `null` Type

Represents the intentional absence of an object value.

```typescript
// With strictNullChecks ON (recommended)
let value: string | null = null;
value = "hello"; // OK
value = null;    // OK

// Without union with null:
let name: string = "Alice";
// name = null; // Error: Type 'null' is not assignable to type 'string'

// Common pattern: nullable values
function findUser(id: number): string | null {
    if (id <= 0) return null;
    return "User " + id;
}
```

## The `undefined` Type

Represents a variable that has been declared but not assigned a value.

```typescript
let notAssigned: undefined = undefined;

// With strictNullChecks ON
let age: number | undefined;
console.log(age); // undefined
age = 25;         // OK

// Optional properties are implicitly string | undefined
interface Config {
    name: string;
    port?: number; // port: number | undefined
}
```

### `null` vs `undefined`

| Aspect       | `null`                              | `undefined`                          |
|--------------|--------------------------------------|--------------------------------------|
| Meaning      | Intentional absence of a value       | Variable not yet assigned             |
| Type         | `null`                               | `undefined`                          |
| Usage        | "This value is empty"                | "This value has not been set"         |
| `typeof`     | `"object"`                           | `"undefined"`                        |

## The `never` Type

`never` represents values that never occur. It is the return type for functions that always throw an error or have infinite loops.

```typescript
// Function that always throws
function throwError(message: string): never {
    throw new Error(message);
}

// Infinite loop
function infiniteLoop(): never {
    while (true) {
        // Never exits
    }
}

// Exhaustive switch checking
type Shape = "circle" | "square" | "triangle";

function getArea(shape: Shape): number {
    switch (shape) {
        case "circle":
            return Math.PI * 1 ** 2;
        case "square":
            return 1 * 1;
        case "triangle":
            return 0.5 * 1 * 1;
        default:
            // If a new shape is added but not handled, this will error
            const exhaustive: never = shape;
            throw new Error(`Unhandled shape: ${exhaustive}`);
    }
}
```

## Using `typeof` for Type Narrowing

```typescript
function process(value: string | number | boolean): void {
    if (typeof value === "string") {
        console.log(value.toUpperCase());
    } else if (typeof value === "number") {
        console.log(value + 10);
    } else {
        console.log(value ? "true" : "false");
    }
}
```

## Analogy

- **`any`** is like a "skip inspection" sticker on a package -- it goes through without any checks, which is fast but risky.
- **`unknown`** is like a sealed package that must be inspected before you know what is inside -- safe but requires checking.
- **`void`** is like signing for a delivery where nothing was delivered -- you acknowledge the action but nothing comes back.
- **`null`** is an intentionally empty box -- you know it is empty and planned it that way.
- **`undefined`** is a box you have not opened yet -- you do not know if anything is inside.
- **`never`** is a mythical creature -- it can never actually exist in reality (a value you will never have).

## Tricky Things to Remember

- `any` is infectious -- using it in a function parameter makes the return type `any` as well.
- `unknown` requires type narrowing (typeof, instanceof, type guards) before use.
- A function returning `void` can still return `undefined`, but not any other value.
- `never` is assignable to every type, but no type is assignable to `never` (except `never` itself).
- With `strictNullChecks: true`, `null` and `undefined` are not assignable to other types without explicit union.
- The `never` type is useful for exhaustive type checking in discriminated unions.

## Interview Questions

1. What is the difference between `any` and `unknown` in TypeScript?
2. When is it acceptable to use the `any` type?
3. How do you safely use a value of type `unknown`?
4. What does the `void` type represent and when is it used?
5. What is the difference between `null` and `undefined` in TypeScript?
6. What is the `never` type and when would you use it?
7. How does `strictNullChecks` affect the behavior of `null` and `undefined`?
8. How can you use `never` for exhaustive type checking?
9. What is the return type of a function that throws an error?
10. Can a `void` function return a value? Why or why not?
