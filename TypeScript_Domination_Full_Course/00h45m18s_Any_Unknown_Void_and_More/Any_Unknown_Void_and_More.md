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

## Expanded Code Examples

### Example 1: When to Use `any` (Migration Scenario)

```typescript
// During migration from JS to TS, you might start with:
function parseConfig(config: any): any {
    // You're not sure what shape the data has yet
    return JSON.parse(config);
}

// Later, you narrow it down:
interface AppConfig {
    appName: string;
    version: string;
    debug: boolean;
    features: string[];
}

function parseConfigTyped(config: string): AppConfig {
    const parsed = JSON.parse(config);
    return parsed as AppConfig; // Assert the known shape
}
```

### Example 2: Safe Use of `unknown` for External Data

```typescript
// API responses are perfect for unknown
async function fetchUserData(userId: string): Promise<unknown> {
    const response = await fetch(`/api/users/${userId}`);
    return response.json();
}

// The caller must narrow the type
interface User {
    id: string;
    name: string;
    email: string;
}

async function displayUser(userId: string): Promise<void> {
    const data: unknown = await fetchUserData(userId);

    // Type guard to narrow unknown
    if (isUser(data)) {
        console.log(`User: ${data.name} (${data.email})`);
    } else {
        console.error("Invalid user data");
    }
}

// Custom type guard
function isUser(data: unknown): data is User {
    return (
        typeof data === "object" &&
        data !== null &&
        "id" in data &&
        "name" in data &&
        "email" in data
    );
}
```

### Example 3: `any` vs `unknown` Comparison

```typescript
function processAny(value: any): void {
    // Any allows everything:
    value.someMethod();
    value + 10;
    value.toUpperCase();
    const x: string = value; // No error, even if value is a number
    // Everything compiles, nothing is safe
}

function processUnknown(value: unknown): void {
    // Unknown requires narrowing:
    // value.someMethod();    // Error!
    // value + 10;            // Error!
    // value.toUpperCase();   // Error!
    // const x: string = value; // Error!

    // Must narrow first:
    if (typeof value === "number") {
        console.log(value + 10); // OK
    } else if (typeof value === "string") {
        console.log(value.toUpperCase()); // OK
    } else if (value && typeof value === "object") {
        console.log(Object.keys(value)); // OK
    }
}
```

### Example 4: Advanced Narrowing for `unknown`

```typescript
function processUnknownData(data: unknown): string {
    // Multiple levels of narrowing
    if (data === null || data === undefined) {
        return "No data";
    }

    if (typeof data === "string") {
        return data.trim();
    }

    if (typeof data === "number") {
        return data.toFixed(2);
    }

    if (Array.isArray(data)) {
        return data.map(item => String(item)).join(", ");
    }

    if (typeof data === "object") {
        try {
            return JSON.stringify(data, null, 2);
        } catch {
            return "Unserializable object";
        }
    }

    // Exhaustive check for remaining types
    return String(data);
}

// Usage with various types
console.log(processUnknownData("  hello  ")); // "hello"
console.log(processUnknownData(42.567));       // "42.57"
console.log(processUnknownData([1, 2, 3]));    // "1, 2, 3"
console.log(processUnknownData({ a: 1 }));     // '{\n  "a": 1\n}'
```

### Example 5: `void` Nuances

```typescript
// void return type -- the function does not return anything useful
function logMessage(message: string): void {
    console.log(message);
    // No return statement
}

// void can return undefined:
function returnUndefined(): void {
    return undefined; // OK
}

function returnVoid(): void {
    return; // OK -- equivalent to return undefined
}

// void does NOT prevent returning undefined:
const result: void = logMessage("test");
console.log(result); // undefined

// But you cannot return a value:
// function badFunction(): void {
//     return 42; // Error: Type 'number' is not assignable to type 'void'
// }

// void in callback context:
function forEachItem<T>(items: T[], callback: (item: T) => void): void {
    for (const item of items) {
        callback(item);
    }
}

// The callback return value (if any) is ignored
forEachItem([1, 2, 3], item => {
    console.log(item);
    // Returning a value is allowed even though void is expected
    return item * 2; // This is ignored by forEachItem
});
```

### Example 6: `void` vs `undefined` in Detail

```typescript
// void is a supertype of undefined
let voidValue: void = undefined; // OK
// let undefinedValue: undefined = voidValue; // Error! void not assignable to undefined

// Function return types:
function returnsUndefined(): undefined {
    return undefined; // Must explicitly return undefined
}

function returnsVoid(): void {
    // No return is fine
    // return undefined is also fine
    // return; is also fine
}

// In practice, use void for functions that have no meaningful return value
// Use undefined only when you specifically need the undefined type

// The never type is different:
function throwsError(): never {
    throw new Error("Always throws");
    // Never reaches the end
}

function infiniteLoop(): never {
    while (true) {
        // Never exits
    }
}
```

### Example 7: `null` and `undefined` with StrictNullChecks

```typescript
// With strictNullChecks: true (recommended)
let name: string = "Alice";
// name = null;      // Error!
// name = undefined; // Error!

// Explicitly nullable:
let nullableName: string | null = "Alice";
nullableName = null;       // OK
// nullableName = undefined; // Error! (unless also added to union)

let fullyOptional: string | null | undefined = "Alice";
fullyOptional = null;       // OK
fullyOptional = undefined;  // OK

// Optional chaining with null/undefined:
interface User {
    name?: string | null;
    address?: {
        street?: string | null;
        city?: string;
    };
}

function getStreetName(user: User): string | null | undefined {
    return user?.address?.street ?? null;
}
```

### Example 8: `null` vs `undefined` Decision Guide

```typescript
// Use undefined for:
let notInitialized: number | undefined; // Variable not yet set
function findItem(id: number): string | undefined; // Item might not exist
interface Config {
    optionalField?: string; // Optional property is string | undefined
}

// Use null for:
function parseData(raw: string): string | null; // Parsing might fail
interface Cache {
    data: string | null; // Cache intentionally cleared
}
// null signals intentional absence; undefined signals "not yet set"

// Practical pattern:
interface UserPreferences {
    theme: "light" | "dark";
    fontSize: number;
    language: string;
    lastSession: Date | null; // null means no previous session
    // Not: lastSession?: Date -- because we want to distinguish
    // "no session" (null) from "property not provided" (undefined)
}
```

### Example 9: `never` for Exhaustive Checks

```typescript
// The never type in discriminated unions
type ApiEvent =
    | { type: "user_login"; userId: string }
    | { type: "user_logout"; userId: string }
    | { type: "error"; message: string }
    | { type: "data_update"; key: string; value: unknown };

function handleEvent(event: ApiEvent): void {
    switch (event.type) {
        case "user_login":
            console.log(`User ${event.userId} logged in`);
            break;
        case "user_logout":
            console.log(`User ${event.userId} logged out`);
            break;
        case "error":
            console.error(`Error: ${event.message}`);
            break;
        case "data_update":
            console.log(`Data updated: ${event.key}`);
            break;
        default:
            // Exhaustiveness check -- if a new event type is added
            // but not handled, this will cause a compile error
            const exhaustive: never = event;
            throw new Error(`Unhandled event type: ${exhaustive}`);
    }
}

// If you add { type: "file_upload"; fileName: string } to ApiEvent,
// the switch will fail to compile because event is not assignable to never
```

### Example 10: Type Guards for Multiple Types

```typescript
// Using typeof type guards
function process(value: string | number | boolean | Date): string {
    if (typeof value === "string") {
        return `String: ${value.toUpperCase()}`;
    }

    if (typeof value === "number") {
        return `Number: ${value.toFixed(2)}`;
    }

    if (typeof value === "boolean") {
        return `Boolean: ${value ? "true" : "false"}`;
    }

    // instanceof guard for objects
    if (value instanceof Date) {
        return `Date: ${value.toISOString()}`;
    }

    // Never reached if all types are handled
    throw new Error("Unexpected type");
}

// Custom type guard
interface Cat { meow(): void; }
interface Dog { bark(): void; }

function isCat(animal: Cat | Dog): animal is Cat {
    return "meow" in animal;
}

function makeSound(animal: Cat | Dog): void {
    if (isCat(animal)) {
        animal.meow();
    } else {
        animal.bark();
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

## More Tricky Scenarios

### Tricky 1: `any` Is Contagious in Functions

```typescript
function getAnything(): any {
    return JSON.parse('{"name": "Alice"}');
}

const data = getAnything(); // data is 'any'
const result = data.name.toUpperCase(); // result is 'any' too!
// Any operation on 'any' returns 'any' -- it spreads everywhere

// Compare with unknown:
function getSafe(): unknown {
    return JSON.parse('{"name": "Alice"}');
}

const safeData = getSafe(); // unknown
// safeData.name.toUpperCase(); // Error -- must narrow first
```

### Tricky 2: `void` Does Not Equal `undefined` in All Contexts

```typescript
// void is assignable FROM undefined:
let v: void = undefined; // OK

// But undefined is NOT assignable FROM void (without strictNullChecks):
// let u: undefined = v; // Error!

// Function return types:
type VoidFn = () => void;
type UndefinedFn = () => undefined;

let fn1: VoidFn = () => 42;        // OK -- return value is ignored
// let fn2: UndefinedFn = () => 42; // Error -- must return undefined
```

### Tricky 3: `null` Is an Object (typeof)

```typescript
console.log(typeof null); // "object" -- this is a JavaScript bug/quirk
// TypeScript handles this correctly:
function check(value: string | null): void {
    if (typeof value === "object") {
        // TypeScript knows this is null, not object
        console.log("It's null!");
    }
}

// For actual object checks:
function isObject(value: unknown): value is Record<string, unknown> {
    return typeof value === "object" && value !== null;
}
```

### Tricky 4: Using `never` in Conditional Types

```typescript
// never helps filter types in conditional types
type NonNullable<T> = T extends null | undefined ? never : T;
// NonNullable<string | null | undefined> = string

type ExtractString<T> = T extends string ? T : never;
// ExtractString<string | number | boolean> = string

// Filtering with never in mapped types
type OnlyStrings<T> = {
    [K in keyof T]: T[K] extends string ? T[K] : never;
}[keyof T];

interface Mixed {
    name: string;
    age: number;
    email: string;
}

type StringKeys = OnlyStrings<Mixed>; // string (union of name and email)
```

## Interview Questions with Answers

### 1. What is the difference between `any` and `unknown` in TypeScript?

Both `any` and `unknown` can hold any value. The key difference is that `unknown` is type-safe: you cannot perform operations on an `unknown` value or assign it to other types without first narrowing its type through type guards (typeof, instanceof, etc.). `any` disables all type checking entirely, allowing any operation and assignment. Use `unknown` when you want the flexibility of `any` but with type safety enforced through narrowing.

### 2. When is it acceptable to use the `any` type?

`any` is acceptable in specific scenarios: during gradual migration from JavaScript to TypeScript (as a temporary measure), when working with third-party libraries that lack type definitions, during rapid prototyping where types are not yet settled, and in rare cases where the type system cannot express the desired behavior. However, every `any` usage should be reviewed and replaced with a proper type when possible.

### 3. How do you safely use a value of type `unknown`?

You must narrow the type before use. Use `typeof` for primitive types (string, number, boolean, object, undefined, function), `instanceof` for class instances, custom type guards with `data is Type` pattern for complex types, and `in` operator checks for object properties. Each narrowing technique allows TypeScript to understand the type within the guarded block and enables safe operations on the value.

### 4. What does the `void` type represent and when is it used?

`void` represents the absence of a meaningful return value. It is typically used as the return type of functions that do not return anything (or whose return value should be ignored). A `void` function can return `undefined` (explicitly or implicitly) but cannot return any other value. It is different from `undefined` in that `void` signals intent ("this function exists for side effects") while `undefined` is a specific value.

### 5. What is the difference between `null` and `undefined` in TypeScript?

`null` represents the intentional absence of an object value -- it is explicitly set to indicate "no value." `undefined` represents a variable that has been declared but not assigned, or a property that does not exist. In TypeScript with `strictNullChecks`, both are distinct types and must be explicitly included in unions to be assignable. Semantically, use `null` for intentionally empty values and `undefined` for not-yet-set values.

### 6. What is the `never` type and when would you use it?

`never` represents values that can never occur. It is used as the return type of functions that always throw errors or have infinite loops. It is also used in exhaustive type checking -- in a switch statement over a discriminated union, the default case can assign the value to a `never` variable, ensuring that adding a new union member causes a compile error until the switch is updated. `never` is also used in conditional types to filter out unwanted types.

### 7. How does `strictNullChecks` affect the behavior of `null` and `undefined`?

With `strictNullChecks: true` (part of `strict: true`), `null` and `undefined` are their own distinct types and cannot be assigned to variables of other types. For example, `let name: string = null;` is an error. You must explicitly use union types like `string | null` or `string | undefined` to allow these values. Without strict mode, `null` and `undefined` are assignable to any type, which can lead to runtime null reference errors.

### 8. How can you use `never` for exhaustive type checking?

In a switch statement over a discriminated union, add a default case that assigns the value to a `never` variable: `const exhaustive: never = value;`. If a new member is added to the union but not handled in the switch, TypeScript will produce a compile error because `value` (with the new type) is not assignable to `never`. This pattern ensures that all union members are explicitly handled.

### 9. What is the return type of a function that throws an error?

A function that always throws an error (or has an infinite loop) has a return type of `never`. This indicates that the function never successfully completes -- it never returns a value. `never` is different from `void` because `void` means the function returns (just with no useful value), while `never` means the function never returns at all. The `never` type also helps TypeScript with control flow analysis after calls to such functions.

### 10. Can a `void` function return a value? Why or why not?

A function declared with `void` return type cannot explicitly return a non-undefined value (`return 42;` would be an error). However, it CAN return `undefined` (either explicitly with `return undefined;` or implicitly with `return;` or just falling off the end). It can also technically return any value in callback contexts, as TypeScript treats `void` as "the return value will be ignored." In practice, `void` signals that the return value is not meaningful.
