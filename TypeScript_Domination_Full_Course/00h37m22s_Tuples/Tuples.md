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

## Type Enforcement in Tuples

```typescript
// Correct: matches the tuple type exactly
let valid: [string, number] = ["hello", 42];

// Error: types are in the wrong order
// let wrong: [string, number] = [42, "hello"];
// Error: Type 'number' is not assignable to type 'string'

// Error: wrong types
// let wrong2: [string, number] = ["hello", "world"];
// Error: Type 'string' is not assignable to type 'number'
```

## Tuple with More Elements

```typescript
// Tuple with three elements
let rgb: [number, number, number] = [255, 0, 0];

// Tuple with mixed types
let response: [number, string, boolean] = [200, "OK", true];
```

## Optional Tuple Elements

```typescript
// Optional element at the end
let config: [string, number?] = ["debug"];
config = ["production", 8080]; // Also valid

// Accessing optional elements
let port: number | undefined = config[1];
```

## Rest Elements in Tuples

```typescript
// A tuple starting with a string, followed by any number of numbers
let scores: [string, ...number[]] = ["Alice", 95, 87, 92, 88];

// Named rest elements
type StringNumberBooleans = [string, number, ...boolean[]];
let data: StringNumberBooleans = ["hello", 42, true, false, true];
```

## Destructuring Tuples

```typescript
let person: [string, number, boolean] = ["Bob", 25, true];

// Destructure with type-safe names
let [name, age, isActive] = person;

console.log(name);     // "Bob"     -- type: string
console.log(age);      // 25        -- type: number
console.log(isActive); // true      -- type: boolean
```

## Tuples vs Arrays

```typescript
// Array -- elements should all be the same type
let numbers: number[] = [1, 2, 3, 4, 5];

// Tuple -- each position has a specific meaning
let httpStatus: [number, string] = [404, "Not Found"];

// Key difference: Tuples know the length and types at each position
// Arrays only know the element type, not the length
```

## Common Use Cases

```typescript
// Returning multiple values from a function
function getMinMax(values: number[]): [number, number] {
    return [Math.min(...values), Math.max(...values)];
}

let [min, max] = getMinMax([3, 1, 4, 1, 5, 9]);
console.log(min); // 1
console.log(max); // 9

// Key-value pairs
let entry: [string, number] = ["age", 30];

// CSV-like data
let csvRow: [string, number, string] = ["John", 28, "Engineer"];
```

## Tricky Cases

```typescript
// This looks like a tuple but is actually an array
let arr: [string, number] = ["a", 1];
// arr.push("extra"); // This actually works at runtime (JS arrays are dynamic)
// But accessing arr[2] would give a type error: Tuple type '[string, number]' has no element at index '2'

// Fix: use readonly tuple to prevent push
let fixed: readonly [string, number] = ["a", 1];
// fixed.push("extra"); // Error: Property 'push' does not exist on type 'readonly [string, number]'
```

## Analogy

A tuple is like a form with labeled fields: the first field is always "Name" (string), the second is always "Age" (number). You cannot enter your age in the Name field. In contrast, an array is like a bag of marbles -- you can put any marble in any position, but they are all marbles. A tuple gives each position specific meaning and type.

## Tricky Things to Remember

- Tuples in TypeScript still compile to regular JavaScript arrays -- the tuple behavior is only enforced at compile time.
- JavaScript array methods like `push` and `pop` still work on tuples at runtime, even though TypeScript may complain.
- Use `readonly` tuples to prevent mutating methods like `push` and `pop`.
- Tuple types are checked structurally -- `[string, number]` is a different shape than `[number, string]`.
- Optional tuple elements must always come at the end of the tuple.

## Interview Questions

1. What is a tuple in TypeScript and how is it different from an array?
2. How do you declare a tuple with three elements of types string, number, and boolean?
3. How do you make an element in a tuple optional?
4. What are rest elements in tuples and how do you use them?
5. How does destructuring work with tuples?
6. Why would you use a tuple over an array?
7. What happens if you call `push()` on a tuple?
8. How do you create a read-only tuple?
9. What is a common use case for tuples in TypeScript?
10. How does TypeScript's structural typing apply to tuples?
