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

## Expanded Code Examples

### Example 1: Comprehensive Variable Annotations

```typescript
// Basic types
let name: string = "Alice";
let age: number = 30;
let isActive: boolean = true;
let data: null = null;
let notDefined: undefined = undefined;

// Union types
let id: string | number = "abc123";
let status: "active" | "inactive" | "pending" = "active";

// Complex types
let user: { name: string; age: number; email: string } = {
    name: "Alice",
    age: 30,
    email: "alice@example.com"
};

// Array types
let scores: number[] = [95, 87, 92];
let mixed: (string | number)[] = [1, "two", 3];

// Tuple types
let pair: [string, number] = ["age", 30];
let rgb: [number, number, number] = [255, 0, 0];

// Function types
let callback: (value: string) => number;
callback = (str) => str.length;

// Any and unknown
let flexible: any = "can be anything";
let safeUnknown: unknown = "must be narrowed first";
```

### Example 2: Function Parameter and Return Annotations

```typescript
// Annotated parameters and return type
function greet(name: string, age: number): string {
    return `${name} is ${age} years old`;
}

// Void return type
function logMessage(message: string): void {
    console.log(message);
    // No return needed
}

// Optional and default parameters
function createUser(
    name: string,
    age?: number,          // optional -- can be omitted
    role: string = "user" // default value
): { name: string; age: number | undefined; role: string } {
    return { name, age, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
    return numbers.reduce((acc, curr) => acc + curr, 0);
}

// Union return type
function findById(id: number): User | null {
    const user = database.find(u => u.id === id);
    return user ?? null;
}
```

### Example 3: Object Annotations

```typescript
// Inline object type
let point: { x: number; y: number } = { x: 10, y: 20 };

// Nested object types
let config: {
    server: {
        host: string;
        port: number;
    };
    database: {
        url: string;
        poolSize: number;
    };
    debug: boolean;
} = {
    server: { host: "localhost", port: 8080 },
    database: { url: "mongodb://localhost", poolSize: 10 },
    debug: true
};

// Object with optional and readonly properties
let settings: {
    readonly appName: string;
    version: string;
    port?: number;
    debug?: boolean;
} = {
    appName: "MyApp",
    version: "1.0.0"
};
// settings.appName = "NewName"; // Error: readonly
```

### Example 4: Function Expression Annotations

```typescript
// Variable with function type annotation
let calculator: (a: number, b: number) => number;
calculator = (x, y) => x + y;

// Generic function type
let transformer: <T>(items: T[], fn: (item: T) => T) => T[];
transformer = (items, fn) => items.map(fn);

// Function type with optional parameters
let formatter: (value: string, uppercase?: boolean) => string;
formatter = (value, uppercase = false) =>
    uppercase ? value.toUpperCase() : value;

// Callback type annotation
function fetchData(
    url: string,
    onSuccess: (data: unknown) => void,
    onError: (error: Error) => void
): void {
    // Implementation
}
```

### Example 5: Union and Intersection Annotations

```typescript
// Union type annotation
let input: string | number | boolean;
input = "hello";  // OK
input = 42;       // OK
input = true;     // OK

// Discriminated union
type Result =
    | { success: true; data: unknown }
    | { success: false; error: string };

function handleResult(result: Result): void {
    if (result.success) {
        console.log(result.data); // Narrowed to success case
    } else {
        console.error(result.error); // Narrowed to error case
    }
}

// Intersection type annotation
type Named = { name: string };
type Aged = { age: number };
let person: Named & Aged = { name: "Alice", age: 30 };
```

### Example 6: Type Assertions (Type Casting)

```typescript
let someValue: unknown = "this is a string";

// Two syntaxes for type assertion
let strLength1: number = (someValue as string).length;
let strLength2: number = (<string>someValue).length;

// Both are equivalent. 'as' syntax is preferred for JSX compatibility.

// Non-null assertion
let nullableValue: string | null = getValue();
// const len = nullableValue.length; // Error: possibly null
const len = nullableValue!.length; // Assert it's non-null

// Const assertion
let colors = ["red", "green", "blue"] as const;
// colors is readonly ["red", "green", "blue"]
```

### Example 7: Advanced Function Signatures

```typescript
// Function overloads
function process(value: string): string;
function process(value: number): number;
function process(value: boolean): boolean;
function process(value: string | number | boolean): string | number | boolean {
    if (typeof value === "string") return value.toUpperCase();
    if (typeof value === "number") return value * 2;
    return !value;
}

// This or void return in callbacks
function forEach<T>(
    items: T[],
    callback: (item: T, index: number) => void
): void {
    for (let i = 0; i < items.length; i++) {
        callback(items[i], i);
    }
}

// Generator function
function* countUpTo(max: number): Generator<number, void, unknown> {
    for (let i = 1; i <= max; i++) {
        yield i;
    }
}

// Async function annotation
async function fetchUser(id: number): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    return response.json();
}
```

### Example 8: This Parameter Annotations

```typescript
// Annotating 'this' in a function
interface Button {
    text: string;
    onClick(this: Button, event: MouseEvent): void;
}

const button: Button = {
    text: "Click me",
    onClick(this: Button, event: MouseEvent) {
        // 'this' is typed as Button
        console.log(this.text);
    }
};

// In callback functions, 'this' can be annotated
function registerClickHandler(
    this: HTMLElement,
    handler: (this: HTMLElement, event: MouseEvent) => void
): void {
    this.addEventListener("click", handler);
}
```

### Example 9: Destructuring with Annotations

```typescript
// Object destructuring with type annotation
function processUser({ name, age }: { name: string; age: number }): void {
    console.log(`${name} is ${age} years old`);
}

// Array destructuring with type annotation
function processScores([first, second, ...rest]: [number, number, ...number[]]): void {
    console.log(`First: ${first}, Second: ${second}, Rest: ${rest.length} more`);
}

// Nested destructuring with annotation
function processConfig({
    server: { host, port },
    database: { url }
}: {
    server: { host: string; port: number };
    database: { url: string };
}): void {
    console.log(`Connecting to ${host}:${port}, DB: ${url}`);
}
```

### Example 10: Mapping and Conditional Type Annotations

```typescript
// Mapped type annotation
type Readonly<T> = {
    readonly [K in keyof T]: T[K];
};

type Optional<T> = {
    [K in keyof T]?: T[K];
};

// Conditional type annotation
type IsString<T> = T extends string ? "yes" : "no";

type Test1: IsString<string>;  // "yes"
type Test2: IsString<number>;  // "no"

// Template literal type annotation
type EventName = `on${Capitalize<string>}`;
type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE";
type APIEndpoint = `/${string}`;

// Indexed access type
interface User {
    name: string;
    age: number;
    email: string;
}

type UserNameType = User["name"];    // string
type UserValueType = User[keyof User]; // string | number
```

## Analogy

Type annotations are like labels on a filing cabinet drawer. The label tells you exactly what belongs inside: "Documents (string)", "Numbers (number)", "Mixed (string | number)". Without labels, you have to open each drawer to see what is inside. With labels, you know before opening -- and the office manager (TypeScript compiler) checks that everyone puts things in the right drawer.

## Tricky Things to Remember

- Annotating a variable with a type is redundant when the initial value clearly implies that type. However, it is useful for clarity and when the variable might later be reassigned with a compatible type.
- Function parameters MUST be annotated if they do not have a default value -- TypeScript does not infer them from usage.
- Optional parameters (marked with `?`) must always come after required parameters in a function signature.
- The return type annotation is often omitted when TypeScript can easily infer it, but it is good practice to include it for public API functions.
- Type annotations are compile-time only -- they are completely erased in the compiled JavaScript.

## More Tricky Scenarios

### Tricky 1: Excess Property Checking with Object Literals

```typescript
interface User {
    name: string;
    age: number;
}

// Object literal gets excess property checking
// const user: User = { name: "Alice", age: 30, email: "alice@test.com" };
// Error: 'email' does not exist in type 'User'

// But assigned from a variable, no excess check
const extra = { name: "Alice", age: 30, email: "alice@test.com" };
const user: User = extra; // OK
```

### Tricky 2: Readonly vs Const in Objects

```typescript
interface Config {
    readonly id: number;
    name: string;
}

let config: Config = { id: 1, name: "test" };
// config.id = 2; // Error: readonly
config.name = "updated"; // OK

// This is different from const:
const config2 = { id: 1, name: "test" };
// config2 = { id: 2, name: "new" }; // Error: const prevents reassignment
config2.name = "updated"; // OK -- const does not prevent property mutation
```

### Tricky 3: Function Overload Resolution

```typescript
// Overload signatures (no body)
function padLeft(value: string, padding: number): string;
function padLeft(value: string, padding: string): string;

// Implementation signature (with body)
function padLeft(value: string, padding: number | string): string {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    return padding + value;
}

// The implementation signature is NOT visible to callers
padLeft("hello", 4);     // OK -- matches first overload
padLeft("hello", "---"); // OK -- matches second overload
// padLeft("hello", true);  // Error -- no overload matches boolean
```

### Tricky 4: Type Assertion vs Type Annotation

```typescript
// Type annotation: check that value conforms to type
const value1: string = someFunction(); // Compiler checks the assignment

// Type assertion: tell the compiler you know better
const value2 = someFunction() as string; // No runtime check either way

// Assertions can lie to the compiler:
const num = 42 as unknown as string; // Compiles but lies about the type
// Use assertions carefully -- they disable type checking
```

## Interview Questions with Answers

### 1. How do you annotate a variable in TypeScript?

Place a colon and the type after the variable name: `let variableName: Type = value;`. For example: `let age: number = 25;`. If the variable has no initial value, the annotation is required: `let age: number;`. Union types are also supported: `let id: string | number = "abc";`. Type annotations can be applied to primitives, objects, arrays, tuples, functions, and any complex type.

### 2. How do you annotate function parameters and return types?

Parameters are annotated inline: `function greet(name: string, age: number): string { ... }`. The return type is placed after the parameter list with a colon. For void functions: `function log(msg: string): void { ... }`. Optional parameters use `?` after the name: `function greet(name: string, age?: number): string`. Default parameters have the type inferred: `function multiply(a: number, b: number = 1): number`.

### 3. What is the difference between `void` and `never` as return type annotations?

`void` means the function returns but has no meaningful return value (it returns `undefined` implicitly or explicitly). `never` means the function never returns -- it always throws an error or has an infinite loop. `void` indicates "no useful value," while `never` indicates "does not complete." `void` is assignable from `undefined`, while `never` is assignable to every type but no type is assignable to `never`.

### 4. How do you create an optional parameter annotation?

Add a `?` after the parameter name before the colon: `function greet(name: string, age?: number): string`. Optional parameters can be omitted when calling the function, and they have the type `Type | undefined` internally. Optional parameters must always come after required parameters in the function signature. You can also use default parameter values as an alternative to optional parameters.

### 5. How do you annotate an object property as read-only?

Use the `readonly` modifier before the property name: `interface Point { readonly x: number; readonly y: number; }`. This prevents reassignment of the property after the object is created. Note that `readonly` does not make the property immutable if it is itself an object -- it only prevents changing which value the property holds. For deep immutability, use `as const` or the `Readonly<T>` utility type.

### 6. What is the syntax for annotating a tuple type?

Use square brackets with comma-separated types: `let pair: [string, number] = ["age", 30];`. Each position in the tuple has a specific type that is enforced. Tuples can have optional elements (`[string, number?]`), rest elements (`[string, ...number[]]`), and labeled elements for documentation (`[name: string, age: number]`). Tuple annotations are checked both for length and types at each position.

### 7. How do you annotate a function expression that is stored in a variable?

Use arrow function syntax with the type: `let fn: (param: Type) => ReturnType = (param) => { ... };`. For example: `let calculator: (a: number, b: number) => number = (x, y) => x + y;`. The variable type annotation specifies the function signature, and the assigned function expression must match that signature. TypeScript will infer parameter types from the annotation when possible.

### 8. What happens to type annotations when TypeScript is compiled to JavaScript?

All type annotations are completely removed during compilation. The compiled JavaScript has no type information. For example, `let age: number = 25;` compiles to `let age = 25;`. Function annotations are also stripped: `function add(a: number, b: number): number { return a + b; }` compiles to `function add(a, b) { return a + b; }`. This process is called "type erasure."

### 9. Why might you annotate the return type of a function even though TypeScript could infer it?

Annotating return types explicitly serves as documentation, catches implementation errors (if the function accidentally returns the wrong type), prevents accidental API changes from affecting consumers, ensures the function's contract is clear, and is required for recursive functions. It is considered a best practice for public API functions, library code, and any function where the return type is not immediately obvious.

### 10. How do you annotate a callback function parameter?

Use an inline function type: `function process(items: number[], callback: (item: number, index: number) => boolean): number[]`. The callback parameter specifies the parameter types and return type of the expected function. You can also define a type alias for reusable callback types: `type FilterFn<T> = (item: T, index: number) => boolean;`. For callbacks that do not return a useful value, use `void`: `(item: T) => void`.
