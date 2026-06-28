# Introduction to Basic Types

## Overview

TypeScript provides a rich set of basic types that allow you to describe the shape and behavior of your data. Understanding these types is the foundation of TypeScript development.

## Categories of Types

### Primitive Types

The building blocks of TypeScript:

- `number` -- all numeric values (integer, float, hex, binary, octal).
- `string` -- textual data (single quotes, double quotes, template literals).
- `boolean` -- `true` or `false`.
- `null` and `undefined` -- represent the absence of a value.
- `symbol` -- unique, immutable values.
- `bigint` -- arbitrarily large integers.

### Reference Types

Types that hold references to data rather than the data itself:

- `object` -- any non-primitive value.
- `array` -- ordered collections of values.
- `function` -- callable objects.

### Special Types

- `any` -- disables type checking (use sparingly).
- `unknown` -- type-safe counterpart of `any`.
- `void` -- absence of a return value (typically for functions).
- `never` -- values that never occur (e.g., functions that always throw).

### Composite Types

- `union` -- a value that can be one of several types (`string | number`).
- `intersection` -- a value that satisfies multiple types (`A & B`).
- `tuple` -- fixed-length arrays with typed elements.

## Expanded Code Examples

### Example 1: Primitive vs Reference Behavior

```typescript
// Primitive types are copied by value
let a: number = 10;
let b: number = a;
b = 20;
console.log(a); // 10 -- unchanged

// Reference types are copied by reference
let arr1: number[] = [1, 2, 3];
let arr2: number[] = arr1;
arr2.push(4);
console.log(arr1); // [1, 2, 3, 4] -- changed!
```

### Example 2: All Primitive Types in Action

```typescript
// String
let firstName: string = "Alice";
let greeting: string = `Hello, ${firstName}!`; // Template literal

// Number
let count: number = 42;
let price: number = 19.99;
let hex: number = 0xff;

// Boolean
let isCompleted: boolean = false;
let hasAccess: boolean = true;

// Null and Undefined
let empty: null = null;
let notAssigned: undefined = undefined;

// Symbol
let uniqueKey: symbol = Symbol("id");

// BigInt
let largeNumber: bigint = 9007199254740991n;
```

### Example 3: Union Types in Practice

```typescript
// A value that can be a string OR a number
let identifier: string | number;
identifier = "ABC-123"; // OK
identifier = 42;        // OK
// identifier = true;   // Error: boolean not allowed

// Union with function parameters
function formatInput(input: string | number): string {
    if (typeof input === "string") {
        return input.toUpperCase();
    }
    return input.toFixed(2);
}

console.log(formatInput("hello")); // "HELLO"
console.log(formatInput(42));      // "42.00"
```

### Example 4: String Literal Union Types

```typescript
// Restrict values to specific strings only
type Direction = "up" | "down" | "left" | "right";
type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE";

function move(direction: Direction): void {
    console.log(`Moving ${direction}`);
}

move("up");     // OK
move("down");   // OK
// move("diagonal"); // Error: not in the union
```

### Example 5: Type Inference with Basic Types

```typescript
// TypeScript infers types from initial values
let name = "Alice";         // inferred as string
let age = 30;               // inferred as number
let isStudent = false;      // inferred as boolean
let items = [1, 2, 3];     // inferred as number[]

// const infers literal types
const constantName = "Alice";   // type: "Alice" (literal)
const constantAge = 30;         // type: 30 (literal)
```

### Example 6: Intersection Types

```typescript
// Combine multiple types into one
type Named = { name: string };
type Aged = { age: number };
type Person = Named & Aged;

let person: Person = {
    name: "Alice",
    age: 30
};
// Person must have BOTH name AND age
```

### Example 7: Tuples for Fixed-Length Arrays

```typescript
// Fixed-length array with typed positions
let httpStatus: [number, string] = [200, "OK"];

// Destructuring a tuple
let [statusCode, statusMessage] = httpStatus;
console.log(statusCode);    // 200 (type: number)
console.log(statusMessage); // "OK" (type: string)
```

### Example 8: The keyof Operator

```typescript
interface User {
    name: string;
    age: number;
    email: string;
}

// keyof creates a union of property names
type UserKey = keyof User; // "name" | "age" | "email"

function getProperty(obj: User, key: UserKey): unknown {
    return obj[key];
}
```

### Example 9: Typeof Type Operator

```typescript
const user = {
    name: "Alice",
    age: 30
};

// typeof extracts the type from a value
type UserType = typeof user;
// Equivalent to: { name: string; age: number; }

let anotherUser: UserType = {
    name: "Bob",
    age: 25
};
```

### Example 10: Type Assertions (Casting)

```typescript
// When you know more about the type than TypeScript does
let someValue: unknown = "this is a string";

// Two syntaxes for type assertion
let strLength1: number = (someValue as string).length;
let strLength2: number = (<string>someValue).length;

// Both are equivalent, but 'as' is preferred for JSX compatibility
```

## Analogy

Primitive types are like individual LEGO bricks -- each one is a single, self-contained piece. Reference types are like a LEGO castle -- when you look at the castle (reference), you see the whole structure, and any change affects the original castle. Composite types (union, intersection, tuple) are like pre-assembled LEGO sections that combine multiple pieces in specific ways.

## Tricky Things to Remember

- In TypeScript, `null` and `undefined` are subtypes of all other types when `strictNullChecks` is off. With strict mode on, they must be explicitly included in union types.
- `any` and `unknown` may look similar, but `unknown` requires type narrowing before use.
- Arrays are objects in JavaScript, and TypeScript still treats them as objects -- `typeof []` returns `"object"`.
- Passing an array to a variable creates a reference, not a copy. Mutating the new variable mutates the original array.
- `const` declarations prevent reassignment of the variable, but not mutation of the value (for objects and arrays).

## More Tricky Scenarios

### Tricky 1: Excess Property Checking on Object Literals

```typescript
interface User {
    name: string;
    age: number;
}

// Object literal assignment -- excess properties cause error
// const user: User = { name: "Alice", age: 30, email: "alice@test.com" };
// Error: Object literal may only specify known properties

// Variable assignment -- no excess property check
const extra = { name: "Alice", age: 30, email: "alice@test.com" };
const user: User = extra; // OK
```

### Tricky 2: Widening Literal Types

```typescript
// const preserves literal type
const x = 10;    // type: 10 (literal)
const y = "hello"; // type: "hello" (literal)

// let widens to base type
let a = 10;      // type: number
let b = "hello"; // type: string

// This matters for type compatibility
let num: number = x; // OK -- 10 is assignable to number
// let lit: 10 = a; // Error -- number is not assignable to 10
```

### Tricky 3: Optional Chaining with Types

```typescript
interface Config {
    server?: {
        host?: string;
        port?: number;
    };
}

function getPort(config: Config): number | undefined {
    // Optional chaining (?.) safely accesses nested optional properties
    return config?.server?.port;
}
```

## Interview Questions with Answers

### 1. What are the primitive types in TypeScript?

The primitive types in TypeScript are: `string` (textual data), `number` (all numeric values including integers, floats, NaN, Infinity), `boolean` (true/false), `null` (intentional absence), `undefined` (unassigned value), `symbol` (unique immutable identifier), and `bigint` (arbitrarily large integers). These are the basic building blocks from which all other types are composed.

### 2. What is the difference between `any` and `unknown`?

Both `any` and `unknown` can hold any value, but `unknown` is type-safe while `any` disables type checking. With `unknown`, you cannot perform operations or assign to other types without first narrowing the type (e.g., with `typeof` checks). With `any`, you can do anything -- which defeats TypeScript's safety. Use `unknown` when you do not know the type upfront but want to maintain type safety.

### 3. How does TypeScript handle `null` and `undefined` in strict mode?

With `strictNullChecks: true` (enabled by `strict: true`), `null` and `undefined` are their own distinct types and are not assignable to other types. You must use union types like `string | null` to allow null values. Without strict mode, `null` and `undefined` are assignable to any type, which can lead to runtime null reference errors.

### 4. What is the difference between value types (primitives) and reference types?

Primitive types (string, number, boolean, etc.) are stored directly in the variable and are copied by value. When you assign a primitive to another variable, a new copy is created. Reference types (objects, arrays, functions) store a reference to the memory location. When you assign a reference type to another variable, both variables point to the same object, and mutations affect the original.

### 5. What is a union type and when would you use one?

A union type (using `|`) allows a value to be one of several types. For example, `string | number` means the value can be either a string or a number. Use union types when a value can legitimately be multiple types, such as a function parameter that accepts both strings and numbers, an API response that can be success data or an error object, or a variable that can hold different types in different states.

### 6. What is a tuple in TypeScript?

A tuple is a fixed-length array where each position has a specific type. For example, `[string, number]` is a tuple where the first element must be a string and the second must be a number. Tuples are useful for representing structured data like key-value pairs, RGB colors, or function return values with multiple components. Unlike arrays, TypeScript tracks the length and types of each position in a tuple.

### 7. What does the `void` type represent?

`void` represents the absence of a return value, typically used as the return type of functions that do not return anything meaningful. A function with `void` return type can return `undefined` (explicitly or implicitly) but cannot return any other value. It is different from `undefined` because `void` is specifically for function return types, while `undefined` is a value and type that any variable can hold.

### 8. What is the `never` type and when is it used?

`never` represents values that never occur. It is used for: functions that always throw an error, functions with infinite loops, and exhaustive type checking in switch statements. `never` is assignable to every type, but no type (except `never` itself) is assignable to `never`. It is useful for ensuring all cases in a discriminated union are handled.

### 9. How does copying a primitive value differ from copying an object reference?

Primitives are copied by value: `let b = a` creates an independent copy of the value. Changing `b` does not affect `a`. Objects/arrays are copied by reference: `let arr2 = arr1` makes both variables point to the same array in memory. Mutating `arr2` also mutates `arr1`. To create an independent copy of an array, use `[...arr]` or `arr.slice()`. For objects, use `{...obj}` or `Object.assign()`.

### 10. Why is `any` considered harmful, and what should you use instead?

`any` disables all type checking for a value, which defeats the purpose of TypeScript. It hides bugs, eliminates IDE support, and spreads through the codebase (operations on `any` return `any`). Instead of `any`, use `unknown` when the type is uncertain (it requires narrowing before use), use proper union types for values with multiple possible types, or use generics when the type should be flexible but still checked.
