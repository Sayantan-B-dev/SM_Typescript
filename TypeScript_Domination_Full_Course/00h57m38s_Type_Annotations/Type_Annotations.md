# Type Annotations

## Overview

Type annotations are explicit declarations that associate a value with a specific type. They serve as documentation and as a contract enforced by the TypeScript compiler.

## Variable Annotations

```typescript
// Basic variable annotation
let isComplete: boolean = false;
let count: number = 42;
let message: string = "Hello";

// Variable without initial value (must annotate)
let age: number;
age = 25; // OK
// age = "twenty-five"; // Error: Type 'string' is not assignable to type 'number'

// Union type annotation
let id: string | number;
id = "abc";  // OK
id = 123;    // OK
// id = true; // Error: Type 'boolean' is not assignable to type 'string | number'
```

## Function Annotations

### Parameter Annotations

```typescript
// Annotated parameters
function greet(name: string, age: number): void {
    console.log(`${name} is ${age} years old`);
}

// Optional parameter
function sayHello(name?: string): void {
    console.log(`Hello, ${name ?? "Guest"}`);
}

// Default parameter
function multiply(a: number, b: number = 1): number {
    return a * b;
}
```

### Return Type Annotations

```typescript
// Explicit return type
function add(a: number, b: number): number {
    return a + b;
}

// Void return type -- no return value
function logError(message: string): void {
    console.error(message);
}

// Never return type -- never returns
function throwError(message: string): never {
    throw new Error(message);
}

// Function returning a union type
function parseInput(input: string): number | null {
    const parsed = Number(input);
    return isNaN(parsed) ? null : parsed;
}
```

### Function Expression Annotations

```typescript
// Variable holding a function with annotation
let calculator: (a: number, b: number) => number;
calculator = (x, y) => x + y;

// Arrow function annotation
const double = (num: number): number => num * 2;

// Inline callback type
function processNumbers(
    numbers: number[],
    callback: (n: number) => boolean
): number[] {
    return numbers.filter(callback);
}
```

## Object Annotations

```typescript
// Inline object type annotation
let user: { name: string; age: number; email: string } = {
    name: "Alice",
    age: 30,
    email: "alice@example.com"
};

// With optional properties
let config: { host: string; port?: number; debug?: boolean } = {
    host: "localhost"
};

// With readonly properties
let point: { readonly x: number; readonly y: number } = {
    x: 10,
    y: 20
};
// point.x = 5; // Error: Cannot assign to 'x' because it is a read-only property
```

## Array and Tuple Annotations

```typescript
// Array annotations
let numbers: number[] = [1, 2, 3];
let strings: Array<string> = ["a", "b", "c"];

// Multi-dimensional array
let matrix: number[][] = [[1, 2], [3, 4]];

// Tuple annotation
let pair: [string, number] = ["age", 30];
let triple: [number, number, number] = [255, 0, 0];
```

## Practical Examples

```typescript
// Annotated function with object parameter and complex return type
interface User {
    id: number;
    name: string;
    role: "admin" | "user" | "guest";
}

function createUser(name: string, role: User["role"]): User {
    return {
        id: Date.now(),
        name,
        role
    };
}

// Annotated callback -- ensures correct usage
function fetchData(url: string, onSuccess: (data: unknown) => void, onError: (error: Error) => void): void {
    // Implementation...
}
```

## Analogy

Type annotations are like labels on a filing cabinet drawer. The label tells you exactly what belongs inside: "Documents (string)", "Numbers (number)", "Mixed (string | number)". Without labels, you have to open each drawer to see what is inside. With labels, you know before opening -- and the office manager (TypeScript compiler) checks that everyone puts things in the right drawer.

## Tricky Things to Remember

- Annotating a variable with a type is redundant when the initial value clearly implies that type. However, it is useful for clarity and when the variable might later be reassigned with a compatible type.
- Function parameters MUST be annotated if they do not have a default value -- TypeScript does not infer them from usage.
- Optional parameters (marked with `?`) must always come after required parameters in a function signature.
- The return type annotation is often omitted when TypeScript can easily infer it, but it is good practice to include it for public API functions.
- Type annotations are compile-time only -- they are completely erased in the compiled JavaScript.

## Interview Questions

1. How do you annotate a variable in TypeScript?
2. How do you annotate function parameters and return types?
3. What is the difference between `void` and `never` as return type annotations?
4. How do you create an optional parameter annotation?
5. How do you annotate an object property as read-only?
6. What is the syntax for annotating a tuple type?
7. How do you annotate a function expression that is stored in a variable?
8. What happens to type annotations when TypeScript is compiled to JavaScript?
9. Why might you annotate the return type of a function even though TypeScript could infer it?
10. How do you annotate a callback function parameter?
