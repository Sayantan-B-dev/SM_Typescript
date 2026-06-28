# Enums

## Overview

Enums (enumerations) allow you to define a set of named constants. They make code more readable and expressive by grouping related values under a common name.

## Numeric Enums (Default)

```typescript
enum Direction {
    Up,      // 0
    Down,    // 1
    Left,    // 2
    Right    // 3
}

// Access by name
let move: Direction = Direction.Up;
console.log(move); // 0

// Reverse mapping (numeric enums only)
console.log(Direction[0]); // "Up"
```

### Custom Starting Value

```typescript
enum StatusCode {
    NotFound = 404,
    InternalServerError = 500,
    BadGateway = 502,
    ServiceUnavailable = 503
}

console.log(StatusCode.NotFound); // 404
```

### Auto-Incrementing from a Start Value

```typescript
enum ErrorLevel {
    Info = 1,    // 1
    Warning,     // 2
    Error,       // 3
    Critical     // 4
}

// Mix of explicit and auto-increment
enum Response {
    No = 0,
    Yes = 1,
    Maybe = 2   // Explicit -- no auto-increment from Yes
}
```

## Expanded Code Examples

### Example 1: Numeric Enum with Computed Values

```typescript
enum FileAccess {
    Read = 1 << 0,      // 1
    Write = 1 << 1,     // 2
    Execute = 1 << 2,   // 4
    ReadWrite = Read | Write,  // 3
    All = Read | Write | Execute  // 7
}

console.log(FileAccess.Read);      // 1
console.log(FileAccess.ReadWrite); // 3
console.log(FileAccess.All);       // 7

// Bitwise operations work with numeric enums
let permission: FileAccess = FileAccess.Read | FileAccess.Write;
console.log(permission); // 3 (ReadWrite)
```

### Example 2: String Enum Patterns

```typescript
enum UserRoles {
    ADMIN = "admin",
    GUEST = "guest",
    SUPER_ADMIN = "super_admin",
    EDITOR = "editor"
}

// String enums are more readable in logs and API responses
function checkPermission(role: UserRoles): boolean {
    return role === UserRoles.ADMIN || role === UserRoles.SUPER_ADMIN;
}

console.log(checkPermission(UserRoles.ADMIN)); // true
console.log(checkPermission(UserRoles.GUEST)); // false

// No reverse mapping:
// console.log(UserRoles["admin"]); // Error: Property 'admin' does not exist
```

### Example 3: Enum as Object Key

```typescript
enum Color {
    Red = "#FF0000",
    Green = "#00FF00",
    Blue = "#0000FF"
}

// Using enum values as object keys
const colorMap: Record<Color, string> = {
    [Color.Red]: "Warm color",
    [Color.Green]: "Nature color",
    [Color.Blue]: "Cool color"
};

console.log(colorMap[Color.Red]); // "Warm color"

// Using enum values in functions
function applyColor(color: Color): void {
    document.body.style.backgroundColor = color;
    console.log(`Applied color: ${color}`);
}
```

### Example 4: Enum with Static Methods

While enums cannot have methods directly, you can use namespaces or helper functions:

```typescript
enum HttpStatus {
    OK = 200,
    Created = 201,
    Accepted = 202,
    BadRequest = 400,
    Unauthorized = 401,
    Forbidden = 403,
    NotFound = 404,
    InternalServerError = 500
}

// Helper namespace (works because enum and namespace share name)
namespace HttpStatus {
    export function isSuccess(status: HttpStatus): boolean {
        return status >= 200 && status < 300;
    }

    export function isClientError(status: HttpStatus): boolean {
        return status >= 400 && status < 500;
    }

    export function isServerError(status: HttpStatus): boolean {
        return status >= 500 && status < 600;
    }

    export function getMessage(status: HttpStatus): string {
        const messages: Record<HttpStatus, string> = {
            [HttpStatus.OK]: "The request succeeded",
            [HttpStatus.Created]: "Resource created",
            [HttpStatus.Accepted]: "Request accepted",
            [HttpStatus.BadRequest]: "Bad request",
            [HttpStatus.Unauthorized]: "Authentication required",
            [HttpStatus.Forbidden]: "Access denied",
            [HttpStatus.NotFound]: "Resource not found",
            [HttpStatus.InternalServerError]: "Internal server error"
        };
        return messages[status] ?? "Unknown status";
    }
}

console.log(HttpStatus.isSuccess(200));  // true
console.log(HttpStatus.isClientError(404)); // true
console.log(HttpStatus.getMessage(200)); // "The request succeeded"
```

### Example 5: Const Enums for Performance

```typescript
// Regular enum -- generates runtime object
enum LogLevel {
    DEBUG,
    INFO,
    WARN,
    ERROR
}

// Const enum -- no runtime object, values are inlined
const enum ConstLogLevel {
    DEBUG,
    INFO,
    WARN,
    ERROR
}

// Regular enum use:
function log(level: LogLevel, message: string): void {
    if (level >= LogLevel.INFO) {
        console.log(message);
    }
}

// Const enum use -- compiles to direct values:
function constLog(level: ConstLogLevel, message: string): void {
    if (level >= 1 /* INFO */) {  // Direct value inlined!
        console.log(message);
    }
}

// Compiled output for constLog:
// function constLog(level, message) {
//     if (level >= 1) {
//         console.log(message);
//     }
// }
```

### Example 6: Ambient Enums (declare enum)

```typescript
// Ambient enum -- declares the enum shape without generating code
// Useful for declaring types from external libraries
declare enum ExternalColors {
    RED = "#FF0000",
    GREEN = "#00FF00",
    BLUE = "#0000FF"
}

// You can use it for type checking:
function paint(color: ExternalColors): void {
    // At runtime, ExternalColors must be provided by the library
}
```

### Example 7: Reverse Mapping Deep Dive

```typescript
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT"
}

// String enums do NOT have reverse mapping
// Direction["UP"] -- Error

// Numeric enum reverse mapping
enum NumericDir {
    Up = 1,
    Down = 2,
    Left = 3,
    Right = 4
}

// Compiled JavaScript for NumericDir:
// var NumericDir;
// (function (NumericDir) {
//     NumericDir[NumericDir["Up"] = 1] = "Up";
//     NumericDir[NumericDir["Down"] = 2] = "Down";
//     NumericDir[NumericDir["Left"] = 3] = "Left";
//     NumericDir[NumericDir["Right"] = 4] = "Right";
// })(NumericDir || (NumericDir = {}));

// Result object:
// { "1": "Up", "2": "Down", "3": "Left", "4": "Right", "Up": 1, "Down": 2, "Left": 3, "Right": 4 }
console.log(NumericDir[1]); // "Up"
console.log(NumericDir["Up"]); // 1
```

### Example 8: Enum Pattern Matching

```typescript
enum Shape {
    Circle = "circle",
    Square = "square",
    Triangle = "triangle",
    Rectangle = "rectangle"
}

function calculateArea(shape: Shape, dimensions: number[]): number {
    switch (shape) {
        case Shape.Circle:
            if (dimensions.length < 1) throw new Error("Radius required");
            return Math.PI * dimensions[0] ** 2;
        case Shape.Square:
            if (dimensions.length < 1) throw new Error("Side length required");
            return dimensions[0] ** 2;
        case Shape.Triangle:
            if (dimensions.length < 2) throw new Error("Base and height required");
            return 0.5 * dimensions[0] * dimensions[1];
        case Shape.Rectangle:
            if (dimensions.length < 2) throw new Error("Width and height required");
            return dimensions[0] * dimensions[1];
        default:
            // Exhaustive check
            const exhaustive: never = shape;
            throw new Error(`Unhandled shape: ${exhaustive}`);
    }
}
```

### Example 9: Flags Enum Pattern

```typescript
enum Permissions {
    None = 0,
    Read = 1 << 0,     // 1
    Write = 1 << 1,    // 2
    Execute = 1 << 2,  // 4
    Delete = 1 << 3,   // 8
    Admin = Read | Write | Execute | Delete  // 15
}

function hasPermission(userPerms: Permissions, required: Permissions): boolean {
    return (userPerms & required) === required;
}

function addPermission(userPerms: Permissions, perm: Permissions): Permissions {
    return userPerms | perm;
}

function removePermission(userPerms: Permissions, perm: Permissions): Permissions {
    return userPerms & ~perm;
}

let myPerms: Permissions = Permissions.Read | Permissions.Write;
console.log(hasPermission(myPerms, Permissions.Read));   // true
console.log(hasPermission(myPerms, Permissions.Execute)); // false

myPerms = addPermission(myPerms, Permissions.Execute);
console.log(hasPermission(myPerms, Permissions.Execute)); // true
```

### Example 10: Enum vs Union Type Decision

```typescript
// When to use enums:
enum Direction {
    North = "NORTH",
    South = "SOUTH",
    East = "EAST",
    West = "WEST"
}

function move(direction: Direction): void {
    // Direction provides reverse mapping, more structured
}

// When to use union of string literals:
type Status = "active" | "inactive" | "pending";

function process(status: Status): void {
    // Union is simpler, no runtime object generated
    // Better for simple string constants
}

// When to use const enum:
const enum YesNo {
    No = 0,
    Yes = 1
}
// Use when you want the readability of an enum
// but zero runtime overhead (values inlined)
```

## How Enums Look After Compilation

A non-const numeric enum compiles to an IIFE (Immediately Invoked Function Expression):

```typescript
// TypeScript:
enum UserRoles {
    ADMIN = "admin",
    GUEST = "guest"
}
```

```javascript
// Compiled JavaScript:
var UserRoles;
(function (UserRoles) {
    UserRoles["ADMIN"] = "admin";
    UserRoles["GUEST"] = "guest";
})(UserRoles || (UserRoles = {}));
```

This creates an object where you can look up values by key or key by value (for numeric enums).

## Analogy

An enum is like a restaurant menu. Instead of saying "I want item number 3," you can say "I want the Caesar Salad" -- the name is more meaningful than the number. Similarly, `Direction.Up` is more readable than `0` in your code. The menu (enum) ensures everyone orders from the same list of valid items.

## Tricky Things to Remember

- Numeric enums have reverse mapping (value-to-name), but string enums do not.
- Const enums cannot have reverse mapping and are inlined at compile time.
- Enums are real objects at runtime (except const enums) -- they add to the bundle size.
- You can pass a raw number to a function expecting a numeric enum -- this is allowed because the enum values are numbers.
- Heterogeneous enums (mixing strings and numbers) are TypeScript-legal but considered poor practice.

## More Tricky Scenarios

### Tricky 1: Numeric Enum Safety Issue

```typescript
enum Direction { Up, Down, Left, Right }

// This works even though 999 is not a valid Direction:
let dir: Direction = 999; // No error!
// Numeric enums are not type-safe at runtime
// You can assign any number to a numeric enum variable

// String enums are safer:
enum SafeDirection { Up = "UP", Down = "DOWN" }
// let sd: SafeDirection = "SOMETHING"; // Error! Type safety is enforced
```

### Tricky 2: Const Enum Cannot Be Used in Ambient Contexts

```typescript
// This works:
declare enum ExternalEnum { A, B, C }

// This does NOT work:
// declare const enum ExternalConstEnum { A, B, C }
// Error: 'const' enums are not allowed in ambient contexts
```

### Tricky 3: Enum Member Initialization Order

```typescript
enum Mixed {
    A = 1,
    B,       // 2 (auto-increments from A)
    C = 10,
    D,       // 11 (auto-increments from C)
    E = A + D, // 12 (computed)
    F,       // 13 (auto-increments from E)
    G = computeNext() // Requires constant-enum context
}

function computeNext(): number {
    return 100;
}

// Enum members with computed values cannot be used in const enums
// const enum Bad {
//     X = Math.random() // Error: const enum member initializer must be a constant expression
// }
```

## Interview Questions with Answers

### 1. What is an enum in TypeScript and why would you use one?

An enum is a way to define a set of named constants, either numeric or string-based. Use enums to group related values under a common name, making code more readable and self-documenting. For example, `Direction.Up` is clearer than `0`, and `HttpStatus.NotFound` is clearer than `404`. Enums also provide type safety by restricting values to only the defined members.

### 2. What is the difference between numeric and string enums?

Numeric enums auto-increment values starting from 0 (or a custom start value) and support reverse mapping (name-to-value and value-to-name). String enums require explicit initialization for every member, do not support auto-incrementing, and do not have reverse mapping. String enums are more readable in logs and API responses since the values are meaningful strings rather than numbers.

### 3. How does reverse mapping work in numeric enums?

Reverse mapping generates a bidirectional object at runtime. The compiled JavaScript assigns both the name-to-value mapping and the value-to-name mapping. For example, `Direction["Up"] = 0` AND `Direction[0] = "Up"]`. This allows looking up the name from the value (`Direction[0]` returns `"Up"`). String enums do not generate reverse mapping because the name would collide with the value (both are strings).

### 4. What is a const enum and how does it differ from a regular enum?

A `const enum` is completely removed during compilation -- its values are inlined wherever they are used. Regular enums generate a runtime IIFE object. Const enums have better performance and smaller bundle size but cannot have reverse mapping or computed members. Use const enums when you want zero runtime overhead and do not need reverse lookup.

### 5. How does a numeric enum get compiled to JavaScript?

A numeric enum compiles to an IIFE that creates a plain object with both forward and reverse mappings. For example, `enum E { A = 1, B = 2 }` compiles to `var E; (function (E) { E[E["A"] = 1] = "A"; E[E["B"] = 2] = "B"; })(E || (E = {}));`. This creates `{ "1": "A", "2": "B", "A": 1, "B": 2 }`. String enums compile similarly but only create forward mappings.

### 6. Can you mix string and numeric values in an enum?

Yes, TypeScript allows heterogeneous enums with both string and numeric members: `enum Mixed { Yes = "YES", No = 0 }`. However, this is not recommended because it defeats the purpose of having a consistent value type and can lead to confusing behavior. Stick to either all strings or all numbers.

### 7. What happens if you pass a number like `404` to a function expecting an `HttpStatus` enum?

It compiles and runs without error because numeric enums are just numbers at runtime. The function parameter typed as `HttpStatus` accepts any number, even if that number does not correspond to a defined enum member. TypeScript does not enforce that numeric enum variables contain only valid enum values. String enums do not have this issue -- they enforce that only the defined string values are accepted.

### 8. Why might you avoid using enums in TypeScript?

Enums add runtime code to the bundle (except const enums), which increases bundle size. They are not tree-shakeable. Some developers prefer union types for simpler cases because they are more lightweight and compile to nothing. Enums also have non-standard behavior compared to other TypeScript features (they are both a type and a value). Consider using `const enum` or union types as alternatives.

### 9. How do you access an enum value by its name and by its value?

Access by name: `Direction.Up` returns `0` (for numeric) or `"UP"` (for string). Access by value (numeric only): `Direction[0]` returns `"Up"`. String enums do not support reverse access by value. You can also iterate over numeric enum keys using `Object.keys(Direction)` which returns both the names and the string representations of values.

### 10. What is the difference between `enum` and `const enum` in terms of runtime behavior?

Regular enums generate a runtime JavaScript object (an IIFE) that exists at runtime, enabling reverse mapping (for numeric) and iteration. Const enums are completely erased during compilation -- every usage of the enum member is replaced with its literal value. Const enums have zero runtime overhead but cannot be used with computed values, cannot be referenced from ambient declarations, and cannot have reverse mapping.
