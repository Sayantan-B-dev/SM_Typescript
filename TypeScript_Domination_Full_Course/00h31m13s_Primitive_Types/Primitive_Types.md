# Primitive Types

## Overview

Primitive types are the fundamental building blocks of TypeScript. They represent simple, immutable values that are built into the language.

## Declaration with var, let, and const

```typescript
// var -- function-scoped, avoid in modern code
var oldWay: number = 10;

// let -- block-scoped, can be reassigned
let current: number = 20;
current = 30; // allowed

// const -- block-scoped, cannot be reassigned
const fixed: number = 40;
// fixed = 50; // Error: Cannot assign to 'fixed' because it is a constant
```

**Why avoid `var`?** `var` has function scoping rather than block scoping, which can lead to unexpected behavior in loops and conditionals.

## The number Type

TypeScript uses a single `number` type for all numeric values -- integers, floating-point, and special numeric values.

```typescript
let integer: number = 42;
let float: number = 3.14;
let negative: number = -10;
let hex: number = 0xff;      // 255 in decimal
let binary: number = 0b1010;  // 10 in decimal
let octal: number = 0o744;    // 484 in decimal
let big: number = 1_000_000;  // Numeric separator for readability
let notANumber: number = NaN;
let infinity: number = Infinity;
```

### Why can't you assign a string to a number variable?

```typescript
let age: number = 25;
// age = "twenty-five"; // Error: Type 'string' is not assignable to type 'number'
```

TypeScript enforces type safety. Once a variable is declared as `number`, it can only hold numeric values. This prevents runtime bugs where a string slips in where a number is expected.

## The boolean Type

Represents `true` or `false`.

```typescript
let isActive: boolean = true;
let isCompleted: boolean = false;
let isGreater: boolean = 10 > 5; // true

// Logical operations
let result: boolean = isActive && isCompleted;
let orResult: boolean = isActive || isCompleted;
let notResult: boolean = !isActive;
```

Common usage: flags, conditional checks, toggles.

## The string Type

Represents textual data. Three ways to define strings:

```typescript
// Single quotes
let single: string = 'Hello';

// Double quotes
let double: string = "World";

// Template literals (backticks) -- support interpolation and multiline
let name: string = "TypeScript";
let greeting: string = `Hello, ${name}!`;
let multiline: string = `
    This is
    a multiline
    string.
`;
```

## Type Inference with Primitives

```typescript
// TypeScript infers the type automatically
let inferredNumber = 42;        // TypeScript infers: number
let inferredString = "hello";   // TypeScript infers: string
let inferredBoolean = true;     // TypeScript infers: boolean

// You can still annotate explicitly
let explicit: number = 42;
```

## Analogy

Primitive types are like the alphabet in a language. Just as letters are the smallest units that combine to form words and sentences, primitive types are the smallest data units that combine to form complex data structures. A `number` is like a specific letter -- it has a distinct identity and cannot be anything else.

## Tricky Things to Remember

- `NaN` is of type `number` in TypeScript, even though it represents "Not a Number."
- `const` with primitives makes the value truly constant (unlike objects where properties can still change).
- Template literals with embedded expressions automatically convert values to strings.
- The numeric separator `_` (1_000_000) is purely for readability -- it has no impact on the value.
- `Infinity` and `-Infinity` are valid `number` values in TypeScript.

## Interview Questions

1. What are the primitive types in TypeScript?
2. What is the difference between `let`, `const`, and `var` for declaring variables?
3. Why should you avoid using `var` in modern TypeScript?
4. What numeric formats does TypeScript support (hex, binary, octal)?
5. Why can't you reassign a string value to a variable declared as `number`?
6. What is the type of `NaN` in TypeScript?
7. How do template literals improve string handling in TypeScript?
8. What is the behavior of `const` with primitive types compared to object types?
9. What is type inference and how does it apply to primitive types?
10. What special numeric values does TypeScript recognize?
