# Introduction to Type Inference and Type Annotations

## Overview

TypeScript has two primary mechanisms for associating types with values: type inference and type annotations. Understanding when to use each is key to writing clean, maintainable TypeScript.

## Type Inference

Type inference is TypeScript's ability to automatically determine the type of a value based on its initial assignment. You do not have to explicitly state the type -- TypeScript figures it out.

```typescript
// TypeScript infers that 'name' is of type 'string'
let name = "Alice";

// TypeScript infers that 'age' is of type 'number'
let age = 30;

// TypeScript infers that 'isActive' is of type 'boolean'
let isActive = true;

// TypeScript infers the return type as 'number'
function add(a: number, b: number) {
    return a + b;
}

// TypeScript infers the type of the array
let numbers = [1, 2, 3]; // inferred as number[]
```

**How inference works:**
- Variable initialization: `let x = 5` infers `x: number`.
- Return statements: The compiler looks at what the function returns.
- Contextual typing: TypeScript uses the context (e.g., event handlers) to infer types.
- Best common type: For arrays like `[1, "hello", true]`, TypeScript finds the best common type `(string | number | boolean)[]`.

## Expanded Code Examples

### Example 1: Inference from Variable Initialization

```typescript
// Basic inference
let count = 0;           // inferred as number
let message = "hello";   // inferred as string
let isDone = false;      // inferred as boolean
let items = [];          // inferred as never[] (empty array)

// Inference with const vs let
const constant = 42;     // inferred as 42 (literal type)
let variable = 42;       // inferred as number (widened)

const userName = "Alice";     // type: "Alice"
let userNameVar = "Alice";    // type: string

// Inference with objects
let user = {
    name: "Alice",
    age: 30
};
// inferred as: { name: string; age: number; }

// Inference with arrays
let mixed = [1, "hello", true];
// inferred as: (string | number | boolean)[]
```

### Example 2: Inference from Return Statements

```typescript
// TypeScript looks at all return statements to infer the return type
function getValue(flag: boolean) {
    if (flag) {
        return "hello";      // string
    }
    return "world";          // string
}
// Inferred return type: string

function getValueMixed(flag: boolean) {
    if (flag) {
        return 42;           // number
    }
    return "default";        // string
}
// Inferred return type: string | number

function getValueOrNull(flag: boolean) {
    if (flag) {
        return "value";
    }
    return null;
}
// Inferred return type: string | null
```

### Example 3: Contextual Typing

```typescript
// The context tells TypeScript what type to expect
const numbers = [1, 2, 3];

// TypeScript knows 'num' is 'number' from the array context
numbers.forEach((num) => {
    console.log(num.toFixed(2)); // OK
});

// Button click event -- TypeScript knows 'event' is MouseEvent
document.querySelector("button")?.addEventListener("click", (event) => {
    console.log(event.clientX); // OK -- event is MouseEvent
});

// Window event -- TypeScript infers the event type
window.addEventListener("keydown", (event) => {
    console.log(event.key); // OK -- event is KeyboardEvent
});

// Contextual typing from assignment target
interface Calculator {
    (a: number, b: number): number;
}

let calc: Calculator = (x, y) => {
    // x and y are inferred as number from Calculator interface
    return x + y;
};
```

### Example 4: Best Common Type Algorithm

```typescript
// TypeScript finds the most specific type that all elements can be
let arr1 = [1, 2, 3];                    // number[]
let arr2 = ["a", "b", "c"];              // string[]
let arr3 = [1, "a", true];               // (string | number | boolean)[]

// With objects, TypeScript finds common properties
let obj1 = { a: 1, b: "hello" };
let obj2 = { a: 2, b: "world" };
let objArr = [obj1, obj2];               // { a: number; b: string; }[]

// More complex best common type
interface Animal { name: string; }
interface Dog extends Animal { breed: string; }
interface Cat extends Animal { color: string; }

let dog: Dog = { name: "Rex", breed: "Labrador" };
let cat: Cat = { name: "Whiskers", color: "black" };

// Best common type is the supertype that both can be assigned to
let pets = [dog, cat];  // Inferred as Animal[] (the common interface)
```

### Example 5: Narrowing with Type Guards

```typescript
function process(value: string | number | Date): void {
    // typeof narrowing
    if (typeof value === "string") {
        // value is string here
        console.log(value.toUpperCase());
    } else if (typeof value === "number") {
        // value is number here
        console.log(value.toFixed(2));
    } else {
        // value is Date here (exhausted the union)
        console.log(value.toISOString());
    }
}

// instanceof narrowing
class MyError extends Error { code: number = 500; }
class ApiError extends Error { status: number = 404; }

function handleError(error: MyError | ApiError): void {
    if (error instanceof MyError) {
        console.log(error.code);
    } else {
        console.log(error.status);
    }
}
```

### Example 6: Literal Type Inference

```typescript
// const preserves literal types
const DIRECTION_UP = "UP";       // type: "UP"
const DIRECTION_DOWN = "DOWN";   // type: "DOWN"
const INITIAL_COUNT = 0;         // type: 0

// let widens to base type
let direction = "UP";            // type: string
let count = 0;                   // type: number

// as const preserves the exact literal type
let config = {
    host: "localhost",
    port: 8080
} as const;
// type: { readonly host: "localhost"; readonly port: 8080; }

// Array with as const
let colors = ["red", "green", "blue"] as const;
// type: readonly ["red", "green", "blue"]
```

### Example 7: Inference with Generics

```typescript
// Generic function -- TypeScript infers the type parameter
function first<T>(items: T[]): T | undefined {
    return items[0];
}

let numResult = first([1, 2, 3]);            // T inferred as number
let strResult = first(["a", "b"]);           // T inferred as string
let mixedResult = first([1, "a", true]);     // T inferred as string | number | boolean

// Generic constraints with inference
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

let user = { name: "Alice", age: 30 };
let userName = getProperty(user, "name");    // T is User, K is "name", return is string
let userAge = getProperty(user, "age");      // return is number
```

### Example 8: When Inference Fails

```typescript
// Recursive functions need explicit return type annotation
// function factorial(n: number) {
//     if (n <= 1) return 1;
//     return n * factorial(n - 1); // Error: recursive reference
// }
// Fix: add explicit return type
function factorial(n: number): number {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}

// Parameters are never inferred from usage
// function multiply(x, y) {
//     return x * y; // x and y are 'any' by default
// }
// Fix: annotate parameters
function multiply(x: number, y: number): number {
    return x * y;
}

// Empty array initialization needs annotation or context
// let items = [];     // inferred as never[]
// items.push("hello"); // Error? Actually works in practice
// Better:
let items: string[] = [];
// or with context:
let nums: number[] = [];
nums.push(1); // OK
```

### Example 9: Inference with Union Types

```typescript
// TypeScript can narrow union types based on control flow
type Shape =
    | { kind: "circle"; radius: number }
    | { kind: "square"; side: number }
    | { kind: "rectangle"; width: number; height: number };

function getArea(shape: Shape): number {
    // TypeScript narrows the type based on the kind property
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.side ** 2;
        case "rectangle":
            return shape.width * shape.height;
    }
}

// Without the discriminated union pattern, narrowing is still possible
function format(value: string | number | boolean): string {
    if (typeof value === "boolean") {
        return value ? "Yes" : "No";
    }
    // value is string | number here
    if (typeof value === "number") {
        return value.toFixed(2);
    }
    // value is string here
    return value.trim();
}
```

### Example 10: Practical Inference Patterns

```typescript
// Destructuring with inference
let point = { x: 10, y: 20 };
let { x, y } = point;           // x: number, y: number

// Array destructuring
let rgb = [255, 0, 0];
let [red, green, blue] = rgb;   // red: number, green: number, blue: number

// Default values with inference
function createUser(name: string, age = 18) {
    return { name, age, createdAt: new Date() };
}
// Inferred return: { name: string; age: number; createdAt: Date; }

// Spread inference
let base = { a: 1, b: 2 };
let extended = { ...base, c: 3, d: "hello" };
// Inferred: { a: number; b: number; c: number; d: string; }

// Conditional inference
let isAdmin = true;
let permissions = isAdmin ? ["read", "write", "delete"] : ["read"];
// Inferred as: string[] (best common type is string[])
```

## Type Annotations

Type annotations are explicit declarations where you tell TypeScript what type a value should be.

```typescript
// Explicit annotation
let name: string = "Alice";
let age: number = 30;
let isActive: boolean = true;

// Function with annotated parameters and return type
function greet(name: string): string {
    return `Hello, ${name}`;
}

// Object with annotated structure
let user: { name: string; age: number } = {
    name: "Alice",
    age: 30
};
```

## Analogy

Type inference is like a GPS that automatically detects your location. You do not need to tell it where you are. Type annotations are like typing a specific address into the GPS -- you are explicit about your destination. The GPS (TypeScript) works well with both approaches, but explicit addresses are safer when the destination is not obvious.

## Tricky Things to Remember

- `const` declarations infer literal types (`const x = 5` infers `x: 5`), while `let` infers the base type (`let x = 5` infers `x: number`).
- TypeScript does not infer return types for recursive functions -- you must annotate them.
- Function parameters are never inferred from usage -- they default to `any` if not annotated.
- Contextual typing flows the type "backward" from the expected type to the expression.
- Inference works best when types are simple and obvious; complex scenarios may need annotations.

## More Tricky Scenarios

### Tricky 1: SSA (Static Single Assignment) Inference

```typescript
let value: string | number;
value = "hello";       // TypeScript narrows to string because of the assignment
value.toUpperCase();   // OK -- value is string here

value = 42;            // TypeScript widens back to the declared union
value.toFixed(2);      // OK -- value is number here

// But you cannot call methods that don't exist on either:
// value.toUpperCase(); // Error at this point -- value is number now
```

### Tricky 2: Inference with Optional Chaining

```typescript
interface Config {
    server?: {
        host?: string;
        port?: number;
    };
}

function getPort(config: Config): number | undefined {
    // Inference works through optional chaining
    return config?.server?.port;
}

// The inferred return type of config?.server?.port is number | undefined
```

### Tricky 3: Inference and Assignment Context

```typescript
// Inference depends on where the value is assigned
let x: number | string = 1;  // Annotated as number | string

// TypeScript remembers the actual assigned value for narrowing
x = "hello";  // x is string now (narrowed from annotation)
x = 42;       // x is number now (switched back)

// But this does not work without annotation:
let y = 1;     // inferred as number
// y = "hello"; // Error! number cannot be string
```

## Interview Questions with Answers

### 1. What is type inference in TypeScript?

Type inference is TypeScript's ability to automatically determine the type of a value based on its initial assignment or usage context without explicit type annotations. For example, `let x = 5` automatically infers `x` as `number`. TypeScript uses several mechanisms for inference: variable initialization, return statements in functions, contextual typing from expected types, and the best common type algorithm for arrays.

### 2. What is the difference between type inference and type annotations?

Type inference automatically determines types without explicit declarations. Type annotations are explicit type declarations written by the developer (e.g., `let x: number = 5`). Inference reduces verbosity and is ideal for obvious cases. Annotations provide explicit documentation, enforce specific types, and are necessary for function parameters, complex types, and scenarios where inference would produce an incorrect or too-wide type.

### 3. When should you use type annotations instead of relying on inference?

Use annotations when: a variable is declared without an initial value, you need a union type that the initial value does not fully represent, you want to enforce a narrower type than what inference would produce, for function parameters and return types (to document the API contract), for complex object types and interfaces, and for recursive functions where inference cannot determine the return type.

### 4. How does TypeScript infer the type of a variable declared with `let` vs `const`?

With `let`, TypeScript infers the base type (e.g., `let x = 5` infers `number`). With `const`, TypeScript infers the literal type (e.g., `const x = 5` infers `5`). This is because `const` cannot be reassigned, so the exact value is the only possible type. `let` can be reassigned, so the wider base type is used. This distinction matters for tuple inference, discriminated unions, and scenarios where exact values are important.

### 5. What is contextual typing in TypeScript?

Contextual typing is TypeScript's ability to infer types "backward" from the expected type in a given context, rather than "forward" from the value. For example, in `numbers.forEach((num) => num.toFixed(2))`, TypeScript knows `num` is `number` because `forEach` on `number[]` expects a callback with `number` parameter. Similarly, event handlers infer the event type from the DOM element context. Contextual typing enables concise code without redundant type annotations.

### 6. Why might you need to annotate function parameters even though TypeScript could infer them?

Function parameters are NOT inferred from function body usage -- TypeScript does not look at how a parameter is used to determine its type. Without an annotation, a parameter defaults to `any`, defeating type safety. Even if TypeScript could theoretically infer the type from usage, explicit annotations are necessary for: documentation (the signature tells consumers the contract), parameter validation at the function boundary, and because inference would require analyzing the function body, which is not how TypeScript works.

### 7. What is the "best common type" algorithm in TypeScript?

When inferring the type of an array with multiple element types, TypeScript finds the "best common type" that all elements can be assigned to. For example, `[1, "hello", true]` is inferred as `(string | number | boolean)[]` because no single type covers all elements, so the union is the best common type. For objects, TypeScript looks for a common supertype or interface that all elements satisfy. For `const` arrays, you can use `as const` to preserve literal types instead of widening.

### 8. Can TypeScript infer return types for recursive functions?

No, TypeScript cannot infer return types for recursive functions. If a function calls itself (directly or indirectly), the compiler cannot determine the return type because it depends on the function itself. You must provide an explicit return type annotation for recursive functions. This is one of the few cases where TypeScript requires an annotation even though the logic might seem straightforward.

### 9. How does inference work with array literals containing mixed types?

TypeScript uses the "best common type" algorithm. For `[1, "hello", true]`, it looks at all element types (`number`, `string`, `boolean`) and finds the most specific type that encompasses all of them, which is `string | number | boolean`. For objects, it finds common properties. For `[1, null]`, the best common type is `number | null`. You can also use `as const` to preserve exact literal types instead of widening.

### 10. What is the trade-off between using inference and annotations?

Inference reduces code verbosity and keeps code clean for obvious types, but it may hide the intended type from readers and can produce unexpected wide types. Annotations provide explicit documentation, enforce contracts, and catch type mismatches at the function boundary, but they add verbosity and redundancy for simple cases. The best practice is: use inference for local variables with obvious types, use annotations for function signatures, public APIs, and any place where the type is not immediately obvious from the context.
