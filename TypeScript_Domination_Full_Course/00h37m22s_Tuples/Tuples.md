# Tuples

## Overview

Tuples are fixed-length arrays where each element has a specific type. Unlike regular arrays where all elements typically share the same type, tuples let you define the type of each element at a specific position.

## Basic Tuple Syntax

```typescript
// A tuple where the first element must be a string and the second must be a number
let person: [string, number] = ["Alice", 30];

// Accessing elements
let name: string = person[0]; // "Alice" -- TypeScript knows this is a string
let age: number = person[1];  // 30 -- TypeScript knows this is a number
```

## Expanded Code Examples

### Example 1: Type Enforcement in Action

```typescript
// Correct: matches the tuple type exactly
let valid: [string, number] = ["hello", 42];

// Error: types are in the wrong order
// let wrong: [string, number] = [42, "hello"];
// Error: Type 'number' is not assignable to type 'string'

// Error: wrong types
// let wrong2: [string, number] = ["hello", "world"];
// Error: Type 'string' is not assignable to type 'number'

// Error: too few elements
// let short: [string, number] = ["hello"];
// Error: Source has 1 element(s) but target requires 2

// Error: too many elements (without rest)
// let extra: [string, number] = ["hello", 42, true];
// Error: Type '[string, number, boolean]' is not assignable to type '[string, number]'
```

### Example 2: Tuples with More Elements

```typescript
// Three-element tuple
let rgb: [number, number, number] = [255, 0, 0];

// Four-element tuple with mixed types
let serverConfig: [string, number, boolean, string] = [
    "production",  // host mode
    8080,           // port
    true,           // SSL enabled
    "api.example.com" // domain
];

// Accessing by index is type-safe
let port: number = serverConfig[1];
let sslEnabled: boolean = serverConfig[2];
```

### Example 3: Optional and Rest Elements

```typescript
// Optional element (must be at the end)
let config: [string, number?] = ["debug"];
config = ["production", 8080]; // Also valid

// Accessing optional element returns union with undefined
let port: number | undefined = config[1];

// Rest element (variable number of trailing elements)
let scores: [string, ...number[]] = ["Alice", 95, 87, 92, 88];

// Multiple named elements before rest
type LogEntry = [Date, string, ...string[]];
let log: LogEntry = [new Date(), "INFO", "User logged in", "User: Alice"];
```

### Example 4: Destructuring Tuples

```typescript
let person: [string, number, boolean] = ["Bob", 25, true];

// Full destructuring
let [name, age, isActive] = person;
console.log(name);     // "Bob"     -- type: string
console.log(age);      // 25        -- type: number
console.log(isActive); // true      -- type: boolean

// Partial destructuring with rest
let [first, ...rest] = person;
console.log(first); // "Bob"     -- type: string
console.log(rest);  // [25, true] -- type: [number, boolean]

// Destructuring with ignored elements
let [username, , active] = ["Charlie", 30, false];
console.log(username); // "Charlie"
console.log(active);   // false
```

### Example 5: Tuples as Function Return Values

```typescript
// Returning multiple values from a function
function getMinMax(values: number[]): [number, number] {
    let min = Math.min(...values);
    let max = Math.max(...values);
    return [min, max];
}

let [min, max] = getMinMax([3, 1, 4, 1, 5, 9]);
console.log(min); // 1
console.log(max); // 9

// Function returning a tuple with different types
function parseUserData(input: string): [string, number, boolean] {
    const parts = input.split(",");
    return [
        parts[0].trim(),
        parseInt(parts[1]),
        parts[2]?.trim() === "active"
    ];
}

let [name, age, active] = parseUserData("Alice,30,active");
```

### Example 6: Labeled Tuples (TypeScript 4+)

```typescript
// Labeled elements improve readability
type HttpResponse = [statusCode: number, statusText: string, headers: Record<string, string>];

function createResponse(body: string): HttpResponse {
    return [
        200,
        "OK",
        { "Content-Type": "text/html", "Content-Length": body.length.toString() }
    ];
}

let [code, text, headers] = createResponse("<html></html>");
console.log(code);    // 200
console.log(text);    // "OK"
console.log(headers); // { "Content-Type": "text/html", "Content-Length": "13" }
```

### Example 7: Tuples vs Arrays Comparison

```typescript
// Array -- elements should all be the same type, variable length
let numbers: number[] = [1, 2, 3, 4, 5];

// Tuple -- each position has a specific meaning, fixed length
let httpStatus: [number, string] = [404, "Not Found"];

// Key differences:
// 1. Tuples know the exact length; arrays do not
// 2. Tuples know the type at each position; arrays know the element type
// 3. Tuples can have different types per position; arrays typically have one type
// 4. Arrays can grow/shrink; tuples have a fixed structure

// Common mistake: using an array where a tuple is more appropriate
// Bad:
let badPair: (string | number)[] = ["age", 30];
// Good:
let goodPair: [string, number] = ["age", 30];
```

### Example 8: Readonly Tuples

```typescript
// Readonly prevents mutation
let readOnlyTuple: readonly [string, number] = ["fixed", 42];
// readOnlyTuple[0] = "changed"; // Error: Index signature in type ... only permits reading
// readOnlyTuple.push("extra"); // Error: Property 'push' does not exist

// Using Readonly utility type
type ReadonlyPair = Readonly<[string, number]>;
let pair: ReadonlyPair = ["hello", 100];

// as const creates a readonly tuple
let constTuple = [1, "hello"] as const;
// type: readonly [1, "hello"] (literal types)
// constTuple[0] = 2; // Error
```

### Example 9: Practical Use Cases

```typescript
// CSV row representation
type CSVRow = [number, string, string, number, boolean];
let row1: CSVRow = [1, "Alice", "Engineer", 75000, true];
let row2: CSVRow = [2, "Bob", "Designer", 65000, false];

// Key-value pair
type KeyValuePair<K extends string | number, V> = [K, V];
let entry: KeyValuePair<string, number> = ["age", 30];
let configEntry: KeyValuePair<string, boolean> = ["debug", true];

// React-like useState return type (simplified)
type UseStateResult<T> = [T, (newValue: T) => void];
function useState<T>(initial: T): UseStateResult<T> {
    let value = initial;
    return [
        value,
        (newValue: T) => { value = newValue; }
    ];
}

let [count, setCount] = useState(0);
// count is type: number
// setCount is type: (newValue: number) => void
```

### Example 10: Tuple Type Inference Challenges

```typescript
// TypeScript may widen array literals
function getPair(): [string, number] {
    return ["hello", 42];
}

// Without explicit return type, this would be inferred as (string | number)[]
function getPairInferred() {
    return ["hello", 42]; // Inferred as (string | number)[], not [string, number]
}

// To force tuple inference without annotation, use as const
function getPairConst() {
    return ["hello", 42] as const; // Inferred as readonly ["hello", 42]
}

// Spread inference
let tuple: [string, number] = ["hello", 42];
let spread: [...typeof tuple, boolean] = [...tuple, true];
// spread is [string, number, boolean]
```

## Analogy

A tuple is like a form with labeled fields: the first field is always "Name" (string), the second is always "Age" (number). You cannot enter your age in the Name field. In contrast, an array is like a bag of marbles -- you can put any marble in any position, but they are all marbles. A tuple gives each position specific meaning and type.

## Tricky Things to Remember

- Tuples in TypeScript still compile to regular JavaScript arrays -- the tuple behavior is only enforced at compile time.
- JavaScript array methods like `push` and `pop` still work on tuples at runtime, even though TypeScript may complain.
- Use `readonly` tuples to prevent mutating methods like `push` and `pop`.
- Tuple types are checked structurally -- `[string, number]` is a different shape than `[number, string]`.
- Optional tuple elements must always come at the end of the tuple.

## More Tricky Scenarios

### Tricky 1: Tuples Allow push/pop at Runtime

```typescript
let tuple: [string, number] = ["hello", 42];
// TypeScript knows this is wrong, but JavaScript allows it:
tuple.push(true);    // No compile error in older TS versions
// Actually, modern TS (4.0+) does catch push with excess elements
// But in some versions: tuple[2] would be accessible but typed as undefined
```

### Tricky 2: Tuple of Union vs Union of Tuple

```typescript
// Tuple of union: each element is string OR number
type A = [string | number, string | number];
// Valid: ["hello", 42], [42, "hello"], [1, 2], ["a", "b"]

// Union of tuples: either [string, number] OR [boolean, boolean]
type B = [string, number] | [boolean, boolean];
// Valid: ["hello", 42] or [true, false]
// Invalid: [42, "hello"] or [1, 2]
```

### Tricky 3: Spread in Tuples

```typescript
// Combining tuples with spread
type A = [number, number];
type B = [string, string];
type C = [...A, ...B]; // [number, number, string, string]

// Prepend/append with tuples
type WithHeader = [string, ...number[]];
// First element is string, rest are numbers

type WithFooter = [...number[], string];
// Variable numbers, then a string at the end
```

## Interview Questions with Answers

### 1. What is a tuple in TypeScript and how is it different from an array?

A tuple is a fixed-length array where each position has a specific, pre-defined type. For example, `[string, number]` means position 0 must be a string and position 1 must be a number. Arrays, by contrast, typically have a uniform type for all elements and variable length. Tuples enforce both the length and the type at each position.

### 2. How do you declare a tuple with three elements of types string, number, and boolean?

`let tuple: [string, number, boolean] = ["hello", 42, true];` The order and types must match exactly. Each position is type-checked independently: position 0 is `string`, position 1 is `number`, position 2 is `boolean`.

### 3. How do you make an element in a tuple optional?

Use the `?` modifier after the type: `[string, number?]` means the second element is optional. Optional elements must always come at the end of the tuple. Accessing an optional element returns a union with `undefined` (e.g., `number | undefined`).

### 4. What are rest elements in tuples and how do you use them?

Rest elements (`...T[]`) allow a tuple to have a variable number of trailing elements of a specified type. For example, `[string, ...number[]]` means the first element is a string, followed by zero or more numbers. This is useful for functions that accept a fixed prefix of parameters plus variable arguments.

### 5. How does destructuring work with tuples?

Tuples can be destructured like arrays. Each variable receives the type of the corresponding tuple position: `let [name, age]: [string, number] = ["Alice", 30];` makes `name: string` and `age: number`. You can also use rest patterns: `let [first, ...rest] = tuple;` and skip elements: `let [a, , c] = tuple;`.

### 6. Why would you use a tuple over an array?

Use tuples when the length is fixed and each position has a specific meaning. Common use cases: returning multiple values from a function, representing key-value pairs, modeling CSV rows, representing HTTP response status pairs [statusCode, message], RGB colors [r, g, b], or any structured data where position implies meaning.

### 7. What happens if you call `push()` on a tuple?

In modern TypeScript (4.0+), calling `push()` on a mutable tuple is allowed in some cases but accessing the pushed element by index outside the declared length is typed as `undefined`. Using `readonly` tuples prevents `push()` entirely at compile time. At runtime, JavaScript arrays always support `push` regardless of TypeScript types.

### 8. How do you create a read-only tuple?

Prefix with `readonly`: `let tuple: readonly [string, number] = ["hello", 42];`. Alternatively, use `Readonly<[string, number]>` or `as const` on the value. Read-only tuples prevent element reassignment and mutating methods, providing stronger guarantees that the tuple structure will not change.

### 9. What is a common use case for tuples in TypeScript?

Returning multiple values from a function is the most common use case. For example, `function getMinMax(values: number[]): [number, number]` returns both the minimum and maximum. Another common use is React's `useState` hook, which returns a tuple of `[value, setter]`. Tuples are also used for key-value pairs, CSV data, and any situation where position indicates meaning.

### 10. How does TypeScript's structural typing apply to tuples?

Tuple types are checked structurally based on their length and element types. `[string, number]` is a different type than `[number, string]` or `[string, number, boolean]`. TypeScript considers the exact shape: the types at each position must match, and the lengths must match (unless rest elements are involved). A tuple is compatible with an array type but not vice versa.
