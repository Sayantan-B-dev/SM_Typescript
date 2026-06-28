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

## When to Use Inference vs Annotations

**Use inference when:**
- The type is obvious from the initial value (`let count = 0`).
- The variable is initialized immediately with a clear type.
- You are declaring local variables in a function.

```typescript
// Good: type is obvious
let count = 0;
let name = "Alice";
let items = [1, 2, 3];
```

**Use annotations when:**
- A variable is declared without an initial value.
- You want to enforce a specific type that is narrower than what inference would give.
- Function parameters and return types (for documentation and safety).
- You need a union type that initial value does not cover.

```typescript
// Annotation needed: no initial value
let age: number;

// Annotation needed: union type not obvious from init
let id: string | number = "abc123";

// Annotation needed: function signature
function process(input: string): number {
    return input.length;
}
```

## Inference vs Annotation: A Comparison

| Aspect                 | Type Inference                       | Type Annotations                     |
|------------------------|---------------------------------------|--------------------------------------|
| Explicit               | No                                    | Yes                                  |
| Code verbosity         | Less verbose                          | More verbose                         |
| Clarity for readers    | Type is implied                       | Type is explicit                     |
| Safety                 | Good for obvious cases                | Better for complex cases             |
| Recommended for        | Local variables, obvious types        | Function signatures, API boundaries  |

## Analogy

Type inference is like a GPS that automatically detects your location. You do not need to tell it where you are. Type annotations are like typing a specific address into the GPS -- you are explicit about your destination. The GPS (TypeScript) works well with both approaches, but explicit addresses are safer when the destination is not obvious.

## Common Inference Patterns

```typescript
// Contextual inference from function parameter types
const numbers = [1, 2, 3];
numbers.forEach((num) => {
    console.log(num.toFixed(2)); // TypeScript knows 'num' is 'number'
});

// Inference from assignment
let result = numbers.map(n => n * 2); // inferred as number[]

// Literal type inference
const constant = "hello";    // inferred as 'hello' (literal type)
let variable = "hello";      // inferred as string (widened)
```

## Tricky Things to Remember

- `const` declarations infer literal types (`const x = 5` infers `x: 5`), while `let` infers the base type (`let x = 5` infers `x: number`).
- TypeScript does not infer return types for recursive functions -- you must annotate them.
- Function parameters are never inferred from usage -- they default to `any` if not annotated.
- Contextual typing flows the type "backward" from the expected type to the expression.
- Inference works best when types are simple and obvious; complex scenarios may need annotations.

## Interview Questions

1. What is type inference in TypeScript?
2. What is the difference between type inference and type annotations?
3. When should you use type annotations instead of relying on inference?
4. How does TypeScript infer the type of a variable declared with `let` vs `const`?
5. What is contextual typing in TypeScript?
6. Why might you need to annotate function parameters even though TypeScript could infer them?
7. What is the "best common type" algorithm in TypeScript?
8. Can TypeScript infer return types for recursive functions?
9. How does inference work with array literals containing mixed types?
10. What is the trade-off between using inference and annotations?
