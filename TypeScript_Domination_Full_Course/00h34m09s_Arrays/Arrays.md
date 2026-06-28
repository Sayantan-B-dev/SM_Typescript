# Arrays

## Overview

Arrays in TypeScript are ordered collections of values. TypeScript allows you to specify what types of values an array can hold, providing compile-time safety.

## Array Syntax

### Type-Specific Arrays

```typescript
// Array of numbers only
let numbers: number[] = [1, 2, 3, 4, 5];

// Alternative syntax using generic Array type
let numbersAlt: Array<number> = [1, 2, 3, 4, 5];

// Array of strings only
let names: string[] = ["Alice", "Bob", "Charlie"];

// Array of booleans only
let flags: boolean[] = [true, false, true];
```

### Mixed-Type Arrays (Union)

```typescript
// Array that can hold numbers or strings
let mixed: (string | number)[] = [1, "two", 3, "four"];

// Without parentheses -- this is different!
// let wrong: string | number[]; // This means: string OR array of numbers
```

When you use `string | number[]`, it means `string` OR `number[]`. To make an array of `string | number`, use parentheses: `(string | number)[]`.

## Type Enforcement

```typescript
let numbers: number[] = [1, 2, 3];

// numbers.push("4"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'

// numbers = [1, "2", 3]; // Error: Type 'string' is not assignable to type 'number'
```

## Tricky Cases with Arrays

```typescript
let arr: number[] = [1, 2, 3, "4"]; // Error: "4" is string, not number
```

Even though `"4"` looks like a number, it is a string literal. TypeScript cares about the type, not the value.

```typescript
let arr: any[] = [1, "2", true, null]; // This works, but defeats type safety
```

## Common Array Methods with Types

```typescript
let numbers: number[] = [1, 2, 3, 4, 5];

// map -- returns a new array with the same length
let doubled: number[] = numbers.map(n => n * 2);

// filter -- returns a subset
let evens: number[] = numbers.filter(n => n % 2 === 0);

// reduce -- returns a single value
let sum: number = numbers.reduce((acc, curr) => acc + curr, 0);

// forEach -- returns void
numbers.forEach(n => console.log(n));

// find -- returns the first match or undefined
let firstEven: number | undefined = numbers.find(n => n % 2 === 0);
```

## Readonly Arrays

```typescript
let readonly: readonly number[] = [1, 2, 3];
// readonly.push(4);    // Error: Property 'push' does not exist on type 'readonly number[]'
// readonly[0] = 10;    // Error: Index signature in type 'readonly number[]' only permits reading

// Alternative using ReadonlyArray
let readonlyAlt: ReadonlyArray<number> = [1, 2, 3];
```

## Multi-Dimensional Arrays

```typescript
// 2D array of numbers
let matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

// 3D array
let cube: number[][][] = [[[1]]];

// Accessing elements
let value: number = matrix[0][1]; // 2
```

## Analogy

An array is like a parking lot with numbered spaces. Each space can hold a specific type of vehicle. `number[]` is a lot where every space must hold a number -- you cannot park a string there. A union array `(string | number)[]` is a lot that accepts either cars or motorcycles. The `readonly` modifier is like a lot with a guard who only lets you look at the cars but never move them.

## Tricky Things to Remember

- `Array<number>` and `number[]` are equivalent -- they are the same type.
- Parentheses matter in union types: `(string | number)[]` vs `string | number[]`.
- `readonly` does not make the elements themselves immutable -- it only prevents adding, removing, or reassigning elements.
- The `length` property of an array is writable in JavaScript, which can truncate arrays. TypeScript does not prevent this.
- `const` with an array does not prevent mutation -- it only prevents reassignment of the variable.

## Interview Questions

1. How do you declare an array of numbers in TypeScript?
2. What is the difference between `number[]` and `[number]`?
3. How do you create an array that accepts both strings and numbers?
4. What is the difference between `(string | number)[]` and `string | number[]`?
5. What does `readonly` do when applied to an array?
6. How do you declare a two-dimensional array in TypeScript?
7. What is the return type of `Array.find()`?
8. How does TypeScript enforce type safety in arrays?
9. What is the difference between `Array<number>` and `number[]`?
10. How do you prevent array mutation in TypeScript?
