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

## Example: Primitive vs Reference Behavior

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

## Analogy

Primitive types are like individual LEGO bricks -- each one is a single, self-contained piece. Reference types are like a LEGO castle -- when you look at the castle (reference), you see the whole structure, and any change affects the original castle. Composite types (union, intersection, tuple) are like pre-assembled LEGO sections that combine multiple pieces in specific ways.

## Tricky Things to Remember

- In TypeScript, `null` and `undefined` are subtypes of all other types when `strictNullChecks` is off. With strict mode on, they must be explicitly included in union types.
- `any` and `unknown` may look similar, but `unknown` requires type narrowing before use.
- Arrays are objects in JavaScript, and TypeScript still treats them as objects -- `typeof []` returns `"object"`.
- Passing an array to a variable creates a reference, not a copy. Mutating the new variable mutates the original array.
- `const` declarations prevent reassignment of the variable, but not mutation of the value (for objects and arrays).

## Interview Questions

1. What are the primitive types in TypeScript?
2. What is the difference between `any` and `unknown`?
3. How does TypeScript handle `null` and `undefined` in strict mode?
4. What is the difference between value types (primitives) and reference types?
5. What is a union type and when would you use one?
6. What is a tuple in TypeScript?
7. What does the `void` type represent?
8. What is the `never` type and when is it used?
9. How does copying a primitive value differ from copying an object reference?
10. Why is `any` considered harmful, and what should you use instead?
