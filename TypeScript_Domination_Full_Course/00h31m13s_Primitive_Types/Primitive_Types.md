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

## Expanded Code Examples

### Example 1: Number Operations

```typescript
let a: number = 10;
let b: number = 3;

// Arithmetic operations
console.log(a + b);  // 13
console.log(a - b);  // 7
console.log(a * b);  // 30
console.log(a / b);  // 3.333...
console.log(a % b);  // 1
console.log(a ** b); // 1000

// Math methods
console.log(Math.round(3.7));    // 4
console.log(Math.floor(3.7));    // 3
console.log(Math.ceil(3.1));     // 4
console.log(Math.abs(-5));       // 5
console.log(Math.max(1, 5, 3));  // 5
console.log(Math.min(1, 5, 3));  // 1
console.log(Math.random());      // Random between 0 and 1
```

### Example 2: Number Parsing and Validation

```typescript
let parsed: number = parseInt("42");         // 42
let parsedFloat: number = parseFloat("3.14"); // 3.14
let fromUnary: number = +"100";              // 100

// NaN detection
let invalid: number = parseInt("hello");
console.log(isNaN(invalid)); // true
console.log(isNaN(42));      // false

// Safe integer check
console.log(Number.isSafeInteger(42));        // true
console.log(Number.isSafeInteger(9999999999999999)); // false
```

### Example 3: String Methods

```typescript
let text: string = "  Hello, TypeScript World!  ";

console.log(text.length);              // 28
console.log(text.toLowerCase());       // "  hello, typescript world!  "
console.log(text.toUpperCase());       // "  HELLO, TYPESCRIPT WORLD!  "
console.log(text.trim());              // "Hello, TypeScript World!"
console.log(text.includes("Type"));    // true
console.log(text.startsWith("  Hel")); // true
console.log(text.endsWith("!  "));     // true
console.log(text.indexOf("Type"));     // 9
console.log(text.slice(2, 7));         // "Hello"
console.log(text.split(" "));          // ["", "", "Hello,", "TypeScript", "World!", "", ""]
console.log(text.replace("TypeScript", "JS")); // "  Hello, JS World!  "
```

### Example 4: Template Literal Advanced Usage

```typescript
let name: string = "Alice";
let age: number = 30;
let role: string = "Developer";

// Multi-line string with embedded expressions
let bio: string = `
    Name: ${name}
    Age: ${age}
    Role: ${role}
    Years until retirement: ${65 - age}
`;

console.log(bio);
// Output:
//     Name: Alice
//     Age: 30
//     Role: Developer
//     Years until retirement: 35

// Tagged template (advanced)
function highlight(strings: TemplateStringsArray, ...values: unknown[]): string {
    return strings.reduce((result, str, i) => {
        return result + str + (values[i] ? `<strong>${values[i]}</strong>` : "");
    }, "");
}

let highlighted: string = highlight`Hello ${name}, you are ${age} years old.`;
console.log(highlighted); // "Hello <strong>Alice</strong>, you are <strong>30</strong> years old."
```

### Example 5: Boolean Short-Circuit Evaluation

```typescript
let isLoggedIn: boolean = true;
let hasPermission: boolean = false;

// AND (&&) -- returns first falsy value or last truthy value
let canAccess: boolean = isLoggedIn && hasPermission; // false

// OR (||) -- returns first truthy value or last falsy value
let defaultRole: string = isLoggedIn || "guest"; // true -- returns true... wait

// Actually || returns the actual value, not a boolean
let roleName: string = isLoggedIn && "admin"; // "admin" (truthy)

// Nullish coalescing (??) -- returns right side only for null/undefined
let username: string | null = null;
let displayName: string = username ?? "Guest"; // "Guest"
```

### Example 6: Scoping with let vs var

```typescript
// var -- function scoped
function varExample(): void {
    for (var i = 0; i < 5; i++) {
        // i is accessible here
    }
    console.log(i); // 5 -- var leaks out of the loop!
}

// let -- block scoped
function letExample(): void {
    for (let i = 0; i < 5; i++) {
        // i is accessible here
    }
    // console.log(i); // Error: i is not defined outside the block
}
```

### Example 7: Type Inference with Primitives

```typescript
// TypeScript infers the type automatically
let inferredNumber = 42;        // TypeScript infers: number
let inferredString = "hello";   // TypeScript infers: string
let inferredBoolean = true;     // TypeScript infers: boolean

// You can still annotate explicitly
let explicit: number = 42;

// Widening with let vs const
let a = 42;        // type: number (widened)
const b = 42;      // type: 42 (literal)

// This matters for things like tuple inference
let tuple: [number, string] = [a, "hello"]; // OK -- a is number
// But with const:
const tuple2: [42, string] = [b, "hello"]; // b is exactly 42
```

### Example 8: Converting Between Types

```typescript
let num: number = 42;
let str: string = String(num);      // "42"
let str2: string = num.toString();   // "42"
let str3: string = `${num}`;         // "42"

let backToNum: number = Number(str); // 42
let backToNum2: number = +str;       // 42
let backToNum3: number = parseInt(str); // 42

// Boolean conversion
let bool: boolean = Boolean(1);   // true
let bool2: boolean = !!1;         // true (double negation)
let bool3: boolean = Boolean(0);  // false
let bool4: boolean = !!0;         // false
let bool5: boolean = Boolean(""); // false
let bool6: boolean = Boolean("hello"); // true
```

### Example 9: Working with NaN and Infinity

```typescript
let result: number = 0 / 0;        // NaN
let infinity: number = 1 / 0;      // Infinity
let negInfinity: number = -1 / 0;  // -Infinity

console.log(isNaN(result));         // true
console.log(isFinite(infinity));    // false
console.log(isFinite(negInfinity)); // false
console.log(isFinite(42));          // true

// Tricky: NaN is not equal to itself
console.log(NaN === NaN);  // false -- use isNaN() instead
console.log(NaN == NaN);   // false

// Number.isNaN vs global isNaN
console.log(isNaN("hello"));       // true -- coerces to number first
console.log(Number.isNaN("hello")); // false -- does not coerce
```

### Example 10: BigInt for Large Numbers

```typescript
// BigInt type for integers beyond Number.MAX_SAFE_INTEGER
let big: bigint = 9007199254740991n;
let big2: bigint = BigInt("9007199254740991");

// Arithmetic with BigInt
let sum: bigint = big + 1n;
let product: bigint = big * 2n;

// Cannot mix BigInt with regular number
// let mixed: bigint = big + 1; // Error!
let mixed: bigint = big + BigInt(1); // OK
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

## More Tricky Scenarios

### Tricky 1: NaN !== NaN

```typescript
const result1: number = NaN;
const result2: number = NaN;
console.log(result1 === result2); // false!
console.log(Object.is(NaN, NaN)); // true -- use Object.is for reliable NaN check
```

### Tricky 2: parseInt Base

```typescript
console.log(parseInt("08"));   // 8 (ES5+ defaults to base 10)
console.log(parseInt("08", 10)); // 8 -- explicit base 10
console.log(parseInt("0x10")); // 16 -- hex detected
console.log(parseInt("10", 2)); // 2 -- binary interpretation
```

### Tricky 3: Floating Point Precision

```typescript
const sum: number = 0.1 + 0.2;
console.log(sum); // 0.30000000000000004 -- not 0.3!
console.log(sum === 0.3); // false

// Fix: use rounding or epsilon comparison
console.log(Math.abs(sum - 0.3) < Number.EPSILON); // true
```

### Tricky 4: String Concatenation vs Addition

```typescript
console.log(1 + 2 + "3");   // "33" -- (1+2)=3, then 3+"3" = "33"
console.log("1" + 2 + 3);   // "123" -- "1"+2="12", then "12"+3="123"
console.log(1 + 2 + 3 + ""); // "6" -- (1+2+3)=6, then 6+"" = "6"
```

## Interview Questions with Answers

### 1. What are the primitive types in TypeScript?

The primitive types in TypeScript are: `string` for textual data, `number` for all numeric values including integers and floats, `boolean` for true/false values, `null` and `undefined` for absent values, `symbol` for unique identifiers, and `bigint` for arbitrarily large integers. These types represent immutable values that are compared by value, not by reference.

### 2. What is the difference between `let`, `const`, and `var` for declaring variables?

`var` is function-scoped and allows redeclaration -- it is outdated and can cause bugs. `let` is block-scoped and cannot be redeclared in the same scope. `const` is block-scoped, cannot be reassigned, and must be initialized at declaration. For primitives, `const` truly makes the value constant. Use `const` by default, `let` when you need reassignment, and never `var` in modern TypeScript.

### 3. Why should you avoid using `var` in modern TypeScript?

`var` has function scoping instead of block scoping, which means it leaks out of loops and if-blocks. It allows redeclaration of the same variable in the same scope, which can silently overwrite values. `var` also has hoisting behavior that can lead to confusion (the declaration is hoisted but not the initialization). `let` and `const` fix all these issues with block scoping and no redeclaration.

### 4. What numeric formats does TypeScript support (hex, binary, octal)?

TypeScript supports decimal (`42`), hexadecimal (`0xFF` as 255), binary (`0b1010` as 10), octal (`0o744` as 484), scientific notation (`1.5e10`), numeric separators for readability (`1_000_000`), and special values like `NaN`, `Infinity`, and `-Infinity`. All these formats produce the `number` type.

### 5. Why can't you reassign a string value to a variable declared as `number`?

TypeScript enforces static type safety. Once a variable is declared as `number`, the compiler ensures only numeric values are assigned. This prevents runtime bugs where arithmetic operations produce unexpected results (e.g., `"5" + 5` produces `"55"` instead of `10`). The type annotation creates a contract that TypeScript enforces at compile time.

### 6. What is the type of `NaN` in TypeScript?

`NaN` (Not a Number) is of type `number` in TypeScript. This is because `NaN` is a numeric value resulting from invalid math operations (like `0/0` or `parseInt("hello")`). It is a quirk inherited from JavaScript. To check for `NaN`, use `isNaN()` or `Number.isNaN()`, not equality checks, because `NaN !== NaN`.

### 7. How do template literals improve string handling in TypeScript?

Template literals (backticks `` ` ``) support: string interpolation with `${expression}`, multi-line strings without concatenation, embedded expressions that are automatically converted to strings, and tagged templates for custom string processing. They make string construction cleaner, more readable, and less error-prone compared to concatenation with `+`.

### 8. What is the behavior of `const` with primitive types compared to object types?

With primitives, `const` makes the value truly immutable -- you cannot reassign or change it. With objects and arrays, `const` only prevents reassignment of the variable; it does not prevent mutation of the object's properties or array's elements. For example, `const arr = [1,2,3]; arr.push(4);` is allowed, but `arr = [4,5,6];` is not.

### 9. What is type inference and how does it apply to primitive types?

Type inference is TypeScript's ability to automatically determine the type of a variable based on its initial value. For primitives, `let x = 42` infers `x: number`, `let y = "hello"` infers `y: string`, and `let z = true` infers `z: boolean`. With `const`, inference produces literal types (`const x = 42` infers `x: 42`), which is narrower than the base type.

### 10. What special numeric values does TypeScript recognize?

TypeScript recognizes `NaN` (result of invalid math), `Infinity` (positive infinity from overflow or division by zero), `-Infinity` (negative infinity), `Number.MAX_VALUE` (largest representable number), `Number.MIN_VALUE` (smallest positive number), `Number.MAX_SAFE_INTEGER` (9007199254740991), and `Number.MIN_SAFE_INTEGER` (-9007199254740991). All of these are of type `number`.
