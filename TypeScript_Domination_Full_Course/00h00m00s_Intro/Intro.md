# Introduction to TypeScript

## What is TypeScript?

TypeScript is an open-source, strongly-typed, object-oriented compiled language developed and maintained by Microsoft. It is a strict superset of JavaScript, meaning any valid JavaScript code is also valid TypeScript code. TypeScript adds optional static typing, classes, interfaces, and other features on top of JavaScript, and compiles down to plain JavaScript that runs anywhere JavaScript runs.

Think of TypeScript as JavaScript with a powerful type system wrapper. If JavaScript is like writing instructions on sticky notes, TypeScript is like writing them in a bound notebook with labeled sections -- the structure helps prevent mistakes before they happen.

## Benefits of TypeScript over JavaScript

- **Static Type Checking**: Catches type-related errors at compile time rather than runtime.
- **Enhanced IDE Support**: Better autocompletion, navigation, and refactoring in editors like VS Code.
- **Self-Documenting Code**: Types serve as inline documentation for what values a function expects and returns.
- **Improved Maintainability**: Large codebases are easier to manage with explicit contracts between components.
- **Early Bug Detection**: A significant percentage of bugs are type-related; TypeScript catches them during development.
- **Modern ECMAScript Features**: TypeScript compiles down to older JS versions, letting you use cutting-edge features today.

## Why Upgrade from JavaScript to TypeScript?

- JavaScript projects become harder to maintain as they grow -- TypeScript scales better.
- Teams collaborating on code benefit from the shared understanding that types provide.
- Refactoring is safer because the compiler catches what breaks.
- TypeScript adoption is massive in the industry; learning it opens doors to React, Angular, Node.js, and more.

## Prerequisites

- Basic knowledge of JavaScript (variables, functions, objects, arrays).
- Familiarity with the command line / terminal.
- A code editor (VS Code recommended).
- Node.js installed on your machine.

## Key Differences Between JavaScript and TypeScript

| Feature               | JavaScript                          | TypeScript                              |
|-----------------------|-------------------------------------|-----------------------------------------|
| Typing                | Dynamic (types determined at runtime)| Static (types checked at compile time) |
| Compilation           | Interpreted directly                 | Compiled to JavaScript                  |
| Type Errors           | Found at runtime                     | Found at compile time                   |
| Interfaces            | Not supported                        | Supported                               |
| Generics              | Not supported                        | Supported                               |
| Enums                 | Not supported                        | Supported                               |
| Decorators            | Stage 2 proposal                     | Supported (with experimental flag)      |

## Type Safety Example

**JavaScript** (no type safety):
```javascript
function add(a, b) {
    return a + b;
}

console.log(add(5, 10));        // 15
console.log(add("5", 10));      // "510" -- concatenation, not addition!
```

**TypeScript** (type safety):
```typescript
function add(a: number, b: number): number {
    return a + b;
}

console.log(add(5, 10));        // 15
// console.log(add("5", 10));   // Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

In JavaScript, the function silently coerces types, leading to bugs. TypeScript prevents this at compile time.

## Features TypeScript Provides That JavaScript Does Not

1. **Static Typing** -- Declare types for variables, parameters, and return values.
2. **Interfaces** -- Define contracts for object shapes.
3. **Type Aliases** -- Create custom names for types.
4. **Generics** -- Write reusable, type-safe code.
5. **Enums** -- Define a set of named constants.
6. **Access Modifiers** -- `public`, `private`, `protected` in classes.
7. **Decorators** -- Annotate and modify classes and members.
8. **Abstract Classes** -- Define base classes that cannot be instantiated.
9. **Union and Intersection Types** -- Combine types in flexible ways.
10. **Type Guards** -- Narrow types within conditional blocks.

## Expanded Code Examples

### Example 1: TypeScript Catches Bugs Early

```typescript
// JavaScript version -- bug slips through
function calculateTotal(price, quantity) {
    return price * quantity;
}
console.log(calculateTotal("10", 2)); // "102" -- string concatenation, not multiplication!

// TypeScript version -- caught at compile time
function calculateTotalSafe(price: number, quantity: number): number {
    return price * quantity;
}
// console.log(calculateTotalSafe("10", 2)); // Error! Compilation fails
```

### Example 2: IDE Intellisense with TypeScript

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    role: "admin" | "user";
}

function displayUser(user: User): string {
    // VS Code will autocomplete: user.id, user.name, user.email, user.role
    return `${user.name} (${user.email}) - ${user.role}`;
}
```

### Example 3: Refactoring Safely

```typescript
// Before refactoring
interface Product {
    id: number;
    title: string;      // old name
    price: number;
}

// After renaming 'title' to 'name'
interface Product {
    id: number;
    name: string;       // new name
    price: number;
}

// TypeScript flags all usages of 'product.title' -- none slip through
```

### Example 4: Self-Documenting Code

```typescript
// Without types, you must guess what this function expects
function fetchData(url, options, callback) {
    // What is options? What does callback return?
}

// With types, the signature is the documentation
function fetchData(
    url: string,
    options: { method: "GET" | "POST"; timeout?: number },
    callback: (error: Error | null, data: unknown) => void
): void {
    // Everything is clear from the signature
}
```

### Example 5: Compiling to Older JavaScript

```typescript
// TypeScript uses optional chaining (?.) and nullish coalescing (??)
// It compiles them down to ES5 compatible code
function getDisplayName(user?: { name?: string | null }): string {
    return user?.name ?? "Anonymous";
}

// Compiled JavaScript (ES5):
// function getDisplayName(user) {
//     var _a;
//     return (_a = user === null || user === void 0 ? void 0 : user.name) !== null && _a !== void 0 ? _a : "Anonymous";
// }
```

### Example 6: Structural Typing in Action

```typescript
// TypeScript uses structural (duck) typing
interface Point2D {
    x: number;
    y: number;
}

interface Point3D {
    x: number;
    y: number;
    z: number;
}

function printPoint(point: Point2D): void {
    console.log(`(${point.x}, ${point.y})`);
}

const point3D: Point3D = { x: 1, y: 2, z: 3 };
printPoint(point3D); // Works! Point3D has all Point2D properties
```

### Example 7: Union and Intersection Benefits

```typescript
// Union type -- value can be one of several types
type Status = "loading" | "success" | "error";

function handleStatus(status: Status): void {
    if (status === "loading") console.log("Loading...");
    else if (status === "success") console.log("Success!");
    else console.log("Error occurred");
}

// Intersection type -- combines multiple types
type HasName = { name: string };
type HasAge = { age: number };
type Person = HasName & HasAge;

// Person must have BOTH name AND age
```

## Tricky Things to Remember

- TypeScript is not a runtime language -- it compiles to JavaScript. Types are erased during compilation.
- A type error in TypeScript does not necessarily mean the compiled JavaScript will fail to run.
- `any` disables type checking -- use it sparingly as it defeats the purpose of TypeScript.
- TypeScript's type system is structural (duck typing), not nominal -- compatibility is based on shape, not name.
- TypeScript can catch type errors, but it cannot catch logic errors or runtime errors like stack overflows.

## More Tricky Scenarios

### Tricky 1: Type Erasure Means No Runtime Type Checking

```typescript
interface User { name: string; }
interface Admin { name: string; role: string; }

function processUser(user: User): void {
    // At runtime, there is NO way to check if 'user' is actually a User
    // TypeScript types do not exist at runtime
    console.log(user.name);
}

const admin = { name: "Alice", role: "admin" };
processUser(admin); // No runtime guard -- this works
```

### Tricky 2: Excess Property Checks

```typescript
interface Config {
    host: string;
    port: number;
}

// Fresh object literal -- excess property check applies
// const config: Config = { host: "localhost", port: 8080, debug: true };
// Error: Object literal may only specify known properties

// But assigned from a variable -- no excess property check
const extra = { host: "localhost", port: 8080, debug: true };
const config: Config = extra; // OK -- excess properties are silently ignored
```

### Tricky 3: any is Contagious

```typescript
function getData(): any {
    return JSON.parse('{"name": "Alice"}');
}

const data = getData(); // data is 'any'
console.log(data.name.toUpperCase()); // No type checking!
// Any operation on 'any' returns 'any', propagating unchecked types
```

## Interview Questions with Answers

### 1. What is TypeScript and how is it different from JavaScript?

TypeScript is a strongly-typed superset of JavaScript developed by Microsoft. It compiles to plain JavaScript. The key difference is that TypeScript adds static type checking at compile time, while JavaScript is dynamically typed and only discovers type errors at runtime. TypeScript also adds features like interfaces, generics, enums, and decorators that JavaScript does not natively support.

### 2. What are the benefits of using TypeScript in a large-scale application?

TypeScript provides static type checking that catches bugs early, improves IDE support with better autocompletion and refactoring, serves as inline documentation through type signatures, makes codebases more maintainable with explicit contracts, and allows teams to refactor with confidence since the compiler catches breaking changes. It also enables using modern ECMAScript features by compiling down to older JavaScript versions.

### 3. Explain the concept of "TypeScript is a superset of JavaScript."

A superset means that any valid JavaScript code is also valid TypeScript code. You can take a `.js` file, rename it to `.ts`, and it will compile without errors (though you may get some type warnings if using strict mode). TypeScript extends JavaScript by adding types and other features on top, so it includes everything JavaScript has and more. The relationship is: JavaScript is a subset of TypeScript.

### 4. How does TypeScript improve the developer experience in editors like VS Code?

TypeScript powers VS Code's IntelliSense, providing autocompletion for properties and methods, showing type information on hover, displaying inline errors as you type, enabling safe refactoring (rename symbol, extract method), and providing go-to-definition and find-all-references functionality. VS Code's JavaScript support is actually powered by the TypeScript engine under the hood.

### 5. What happens to TypeScript types when code is compiled to JavaScript?

All TypeScript-specific syntax -- type annotations, interfaces, type aliases, generics, enums (except const enums), and access modifiers -- are completely stripped during compilation. The output is clean JavaScript with no type information. This process is called "type erasure." For example, `function add(a: number, b: number): number { return a + b; }` compiles to `function add(a, b) { return a + b; }`.

### 6. Explain the difference between static and dynamic typing.

Static typing means types are checked at compile time, before the code runs. Variables have fixed types that cannot change. Dynamic typing means types are checked at runtime, and variables can hold values of any type at any time. TypeScript uses static typing; JavaScript uses dynamic typing. Static typing catches type errors earlier (during development), while dynamic typing offers more flexibility but can hide bugs until they crash at runtime.

### 7. Why is `any` considered harmful in TypeScript?

`any` disables all type checking for a value, which defeats the primary purpose of TypeScript. It makes type errors that would be caught at compile time become runtime errors instead. `any` is contagious -- operations on `any` return `any`, spreading unchecked types through the codebase. It makes code harder to understand and maintain because there is no documentation about what type a value should be. Prefer `unknown` when the type is truly uncertain, as it requires type narrowing before use.

### 8. What are some features TypeScript has that JavaScript does not?

TypeScript adds: static type annotations, interfaces, type aliases, generics, enums, access modifiers (public, private, protected), abstract classes, decorators (behind a flag), union types, intersection types, type guards, mapped types, conditional types, tuple types with labeled elements, const assertions, and namespace support. Many of these are compile-time only and do not exist at runtime.

### 9. How does TypeScript help with refactoring large codebases?

TypeScript provides safe refactoring tools: renaming a symbol (function, variable, property) automatically renames all usages; changing an interface's shape immediately shows all objects that no longer conform; removing a function parameter flags all call sites; the compiler catches type mismatches across the entire codebase in one pass. This gives developers confidence to make sweeping changes without fear of missing something.

### 10. What is the compilation process from TypeScript to JavaScript?

The TypeScript compiler (`tsc`) parses `.ts` files into an Abstract Syntax Tree (AST), performs type checking based on the AST and type definitions, reports any type errors, and then emits JavaScript code by stripping all TypeScript-specific syntax. The compilation process respects the `tsconfig.json` settings for target ECMAScript version, module system, output directory, and other options. The output is plain JavaScript that runs in any environment that supports the target ECMAScript version.
