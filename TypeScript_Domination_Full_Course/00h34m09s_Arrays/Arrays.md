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

## Expanded Code Examples

### Example 1: Array Creation Methods

```typescript
// Literal syntax
let fruits: string[] = ["apple", "banana", "orange"];

// Array constructor (be careful -- this creates [empty x 5] for single number)
let empty: number[] = new Array(5);   // [empty x 5]
let filled: number[] = new Array(1, 2, 3); // [1, 2, 3]

// Array.of
let numbers: number[] = Array.of(1, 2, 3, 4, 5);

// Array.from
let fromString: string[] = Array.from("hello"); // ["h", "e", "l", "l", "o"]
let mapped: number[] = Array.from([1, 2, 3], x => x * 2); // [2, 4, 6]

// Spread operator
let original: number[] = [1, 2, 3];
let copy: number[] = [...original];   // [1, 2, 3] (independent copy)
let combined: number[] = [...original, 4, 5]; // [1, 2, 3, 4, 5]
```

### Example 2: Adding and Removing Elements

```typescript
let numbers: number[] = [1, 2, 3];

// Add to end
numbers.push(4);           // [1, 2, 3, 4]

// Remove from end
let last: number | undefined = numbers.pop();  // 4, numbers: [1, 2, 3]

// Add to beginning
numbers.unshift(0);        // [0, 1, 2, 3]

// Remove from beginning
let first: number | undefined = numbers.shift(); // 0, numbers: [1, 2, 3]

// Add/remove at any position (splice)
numbers.splice(1, 0, 1.5); // Insert 1.5 at index 1: [1, 1.5, 2, 3]
numbers.splice(1, 1);      // Remove 1 element at index 1: [1, 2, 3]

// Replace
numbers.splice(1, 1, 99);  // Replace index 1 with 99: [1, 99, 3]
```

### Example 3: Array Iteration Methods

```typescript
let numbers: number[] = [1, 2, 3, 4, 5];

// forEach -- iterate without returning
numbers.forEach((num, index) => {
    console.log(`Index ${index}: ${num}`);
});

// map -- transform each element, returns new array
let doubled: number[] = numbers.map(n => n * 2); // [2, 4, 6, 8, 10]

// filter -- keep elements that pass the test
let evens: number[] = numbers.filter(n => n % 2 === 0); // [2, 4]

// reduce -- accumulate to a single value
let sum: number = numbers.reduce((acc, curr) => acc + curr, 0); // 15

// reduceRight -- same as reduce but right to left
let concatenated: string = numbers.reduceRight(
    (acc, curr) => acc + "-" + curr.toString(), ""
); // "-5-4-3-2-1"
```

### Example 4: Finding Elements

```typescript
let fruits: string[] = ["apple", "banana", "orange", "banana"];

// indexOf -- first index of value (-1 if not found)
let firstBanana: number = fruits.indexOf("banana");     // 1

// lastIndexOf -- last index of value
let lastBanana: number = fruits.lastIndexOf("banana");  // 3

// includes -- boolean check
let hasApple: boolean = fruits.includes("apple");       // true

// find -- first element satisfying condition
let found: string | undefined = fruits.find(f => f.startsWith("b")); // "banana"

// findIndex -- index of first element satisfying condition
let foundIndex: number = fruits.findIndex(f => f.startsWith("b")); // 1

// some -- does any element satisfy condition?
let hasLongName: boolean = fruits.some(f => f.length > 6); // true

// every -- do all elements satisfy condition?
let allLong: boolean = fruits.every(f => f.length > 4);   // true
```

### Example 5: Sorting and Reversing

```typescript
let numbers: number[] = [3, 1, 4, 1, 5, 9, 2, 6];

// sort (in-place, converts to strings by default)
numbers.sort();
console.log(numbers); // [1, 1, 2, 3, 4, 5, 6, 9] -- works for numbers

// Custom comparator for descending
numbers.sort((a, b) => b - a);
console.log(numbers); // [9, 6, 5, 4, 3, 2, 1, 1]

// Strings
let names: string[] = ["Charlie", "Alice", "Bob"];
names.sort();
console.log(names); // ["Alice", "Bob", "Charlie"]

// reverse
names.reverse();
console.log(names); // ["Charlie", "Bob", "Alice"]

// toSorted (ES2023 -- returns new sorted array, doesn't modify original)
let sorted: number[] = numbers.toSorted((a, b) => a - b);
```

### Example 6: Flattening and Concatenating

```typescript
let nested: number[][] = [[1, 2], [3, 4], [5, 6]];

// flat -- flatten one level
let flat: number[] = nested.flat(); // [1, 2, 3, 4, 5, 6]

// Deep flatten
let deepNested: any[] = [1, [2, [3, [4]]]];
let deepFlat: number[] = deepNested.flat(Infinity); // [1, 2, 3, 4]

// flatMap -- map then flatten one level
let phrases: string[] = ["hello world", "foo bar"];
let words: string[] = phrases.flatMap(p => p.split(" ")); // ["hello", "world", "foo", "bar"]

// concat
let arr1: number[] = [1, 2];
let arr2: number[] = [3, 4];
let combined: number[] = arr1.concat(arr2); // [1, 2, 3, 4]
```

### Example 7: Array Destructuring

```typescript
let numbers: number[] = [1, 2, 3, 4, 5];

// Basic destructuring
let [first, second, ...rest] = numbers;
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]

// Skipping elements
let [a, , c] = numbers;
console.log(a); // 1
console.log(c); // 3

// Default values
let [x = 0, y = 0, z = 0] = [1, 2];
console.log(z); // 0 (default)

// Swapping
let p: number = 1, q: number = 2;
[p, q] = [q, p];
console.log(p); // 2
console.log(q); // 1
```

### Example 8: Readonly and Immutable Patterns

```typescript
// Readonly array -- prevents mutation
let readonly: readonly number[] = [1, 2, 3];
// readonly.push(4);    // Error
// readonly[0] = 10;    // Error
// readonly.pop();      // Error

// ReadonlyArray generic
let readonlyAlt: ReadonlyArray<number> = [1, 2, 3];

// Immutable operations return new arrays
let mutable: number[] = [1, 2, 3];
let updated: number[] = [...mutable, 4];   // [1, 2, 3, 4]
let without: number[] = mutable.filter(n => n !== 2); // [1, 3]
let replaced: number[] = mutable.map(n => n === 2 ? 99 : n); // [1, 99, 3]

// as const for deeply readonly
let constArray = [1, 2, 3] as const;
// constArray[0] = 10; // Error: Cannot assign to '0' because it is a read-only property
```

### Example 9: Multi-Dimensional Arrays

```typescript
// 2D array (matrix)
let matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

// Accessing elements
let center: number = matrix[1][1]; // 5

// Iterating over 2D array
for (let row of matrix) {
    for (let cell of row) {
        console.log(cell);
    }
}

// 3D array
let cube: number[][][] = [
    [[1, 2], [3, 4]],
    [[5, 6], [7, 8]]
];

// Jagged array (rows have different lengths)
let jagged: number[][] = [
    [1, 2],
    [3, 4, 5],
    [6]
];
```

### Example 10: Typed Array Methods and Chaining

```typescript
interface User {
    id: number;
    name: string;
    age: number;
    active: boolean;
}

const users: User[] = [
    { id: 1, name: "Alice", age: 30, active: true },
    { id: 2, name: "Bob", age: 25, active: false },
    { id: 3, name: "Charlie", age: 35, active: true },
    { id: 4, name: "Diana", age: 28, active: true }
];

// Method chaining with type safety
let result: string[] = users
    .filter(user => user.active)                    // User[]
    .filter(user => user.age >= 30)                 // User[]
    .map(user => user.name.toUpperCase())           // string[]
    .sort();                                        // string[]

console.log(result); // ["ALICE", "CHARLIE"]
```

## Analogy

An array is like a parking lot with numbered spaces. Each space can hold a specific type of vehicle. `number[]` is a lot where every space must hold a number -- you cannot park a string there. A union array `(string | number)[]` is a lot that accepts either cars or motorcycles. The `readonly` modifier is like a lot with a guard who only lets you look at the cars but never move them.

## Tricky Things to Remember

- `Array<number>` and `number[]` are equivalent -- they are the same type.
- Parentheses matter in union types: `(string | number)[]` vs `string | number[]`.
- `readonly` does not make the elements themselves immutable -- it only prevents adding, removing, or reassigning elements.
- The `length` property of an array is writable in JavaScript, which can truncate arrays. TypeScript does not prevent this.
- `const` with an array does not prevent mutation -- it only prevents reassignment of the variable.

## More Tricky Scenarios

### Tricky 1: Array.sort() Converts to Strings

```typescript
let numbers: number[] = [1, 10, 2, 20];
numbers.sort();
console.log(numbers); // [1, 10, 2, 20] -- wrong! Sorted as strings

// Correct:
numbers.sort((a, b) => a - b);
console.log(numbers); // [1, 2, 10, 20]
```

### Tricky 2: Empty Array Type Inference

```typescript
let empty = []; // Type: never[] -- cannot be used safely
empty.push(1);  // Now it's number[] (inferred from usage)

// Better: annotate explicitly
let items: number[] = [];
items.push(1);  // OK
```

### Tricky 3: Array Methods That Mutate vs Return New

```typescript
// Mutate original:
arr.sort()      // In-place sort
arr.reverse()   // In-place reverse
arr.push()      // Adds to end
arr.pop()       // Removes from end
arr.splice()    // Add/remove at index

// Return new array:
arr.map()       // Transform
arr.filter()    // Subset
arr.concat()    // Combine
arr.slice()     // Subarray (copy)
arr.flat()      // Flatten
[...arr]        // Spread copy
```

### Tricky 4: Sparse Arrays

```typescript
let sparse: number[] = [1, , 3]; // [1, empty, 3]
console.log(sparse[1]); // undefined
console.log(sparse.length); // 3

// forEach skips empty slots
sparse.forEach(n => console.log(n)); // Only logs 1 and 3

// Array constructor with single number
let fiveEmpty = new Array(5); // [empty x 5] -- no elements, just length
console.log(fiveEmpty.length); // 5
```

## Interview Questions with Answers

### 1. How do you declare an array of numbers in TypeScript?

Use `number[]` syntax: `let numbers: number[] = [1, 2, 3];` or the generic syntax: `let numbers: Array<number> = [1, 2, 3];`. Both are equivalent. The `number[]` syntax is more common and idiomatic.

### 2. What is the difference between `number[]` and `[number]`?

`number[]` is an array of numbers with variable length. `[number]` is a tuple with exactly one element that must be a number. `[number]` is a fixed-length array where TypeScript enforces that there is exactly one element of type number.

### 3. How do you create an array that accepts both strings and numbers?

Use a union type in parentheses: `let mixed: (string | number)[] = [1, "hello", 42];`. The parentheses are crucial; without them, `string | number[]` means "string OR array of numbers," which is different.

### 4. What is the difference between `(string | number)[]` and `string | number[]`?

`(string | number)[]` is an array where each element can be either a string or a number (elements have type `string | number`). `string | number[]` is a union type meaning the value can be either a single string or an array of numbers. The parentheses make the union apply to the array elements rather than to the array itself.

### 5. What does `readonly` do when applied to an array?

`readonly` prevents mutation of the array structure: you cannot push, pop, splice, or reassign elements by index. However, it does not make the elements themselves immutable -- if the elements are objects, their properties can still be changed. `readonly` is a compile-time check, not a runtime guarantee.

### 6. How do you declare a two-dimensional array in TypeScript?

Use `number[][]` for a 2D array of numbers: `let matrix: number[][] = [[1,2], [3,4], [5,6]];`. Each nested array must be of type `number[]`. For more complex nested types, you can use `Array<Array<number>>` which is equivalent.

### 7. What is the return type of `Array.find()`?

`Array.find()` returns `T | undefined` where `T` is the element type of the array. For example, `[1,2,3].find(n => n > 1)` returns `number | undefined`. It returns `undefined` when no element satisfies the predicate, so you should handle the possibly-undefined value before using it.

### 8. How does TypeScript enforce type safety in arrays?

TypeScript prevents adding elements of incorrect types to typed arrays (`arr.push("string")` on `number[]` is an error), assigning arrays of different types (`string[]` to `number[]` is an error), and passing arrays to functions expecting different types. It also provides type information when accessing elements (e.g., `arr[0]` is typed as `number` for `number[]`).

### 9. What is the difference between `Array<number>` and `number[]`?

There is no difference. `Array<number>` and `number[]` are completely equivalent syntaxes for the same type. `number[]` is more common and concise. `Array<number>` uses the generic type syntax and may be preferred when the element type is more complex (e.g., `Array<{ name: string; age: number }>`).

### 10. How do you prevent array mutation in TypeScript?

Use `readonly number[]` or `ReadonlyArray<number>` to prevent push, pop, splice, and index assignment. Use `as const` for deeply immutable arrays: `const arr = [1, 2, 3] as const;` makes the entire array read-only and the elements literal types. For a fully immutable pattern, combine readonly with spread operations that return new arrays instead of mutating.
