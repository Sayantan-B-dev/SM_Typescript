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
```

## String Enums

```typescript
enum UserRoles {
    ADMIN = "admin",
    GUEST = "guest",
    SUPER_ADMIN = "super_admin",
    EDITOR = "editor"
}

// Access
let role: UserRoles = UserRoles.ADMIN;
console.log(role); // "admin"

// No reverse mapping for string enums
// console.log(UserRoles["admin"]); // Error
```

## Heterogeneous Enums (Mixed)

Mixing string and numeric values is possible but not recommended:

```typescript
enum Mixed {
    Yes = "YES",
    No = 0
}
```

## Const Enums

Const enums are completely removed during compilation, with their values inlined:

```typescript
const enum Color {
    Red = "#FF0000",
    Green = "#00FF00",
    Blue = "#0000FF"
}

// At runtime, this becomes: console.log("#FF0000")
console.log(Color.Red);
```

Compiled output:
```javascript
// No enum object exists
console.log("#FF0000" /* Red */);
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

## Practical Example

```typescript
enum HttpStatus {
    OK = 200,
    Created = 201,
    BadRequest = 400,
    Unauthorized = 401,
    NotFound = 404,
    InternalServerError = 500
}

function handleResponse(status: HttpStatus): void {
    switch (status) {
        case HttpStatus.OK:
            console.log("Request succeeded");
            break;
        case HttpStatus.NotFound:
            console.log("Resource not found");
            break;
        case HttpStatus.InternalServerError:
            console.log("Server error");
            break;
        default:
            console.log("Unknown status");
    }
}

handleResponse(HttpStatus.OK); // "Request succeeded"
```

## Analogy

An enum is like a restaurant menu. Instead of saying "I want item number 3," you can say "I want the Caesar Salad" -- the name is more meaningful than the number. Similarly, `Direction.Up` is more readable than `0` in your code. The menu (enum) ensures everyone orders from the same list of valid items.

## Tricky Things to Remember

- Numeric enums have reverse mapping (value-to-name), but string enums do not.
- Const enums cannot have reverse mapping and are inlined at compile time.
- Enums are real objects at runtime (except const enums) -- they add to the bundle size.
- You can pass a raw number to a function expecting a numeric enum -- this is allowed because the enum values are numbers.
- Heterogeneous enums (mixing strings and numbers) are TypeScript-legal but considered poor practice.

## Interview Questions

1. What is an enum in TypeScript and why would you use one?
2. What is the difference between numeric and string enums?
3. How does reverse mapping work in numeric enums?
4. What is a const enum and how does it differ from a regular enum?
5. How does a numeric enum get compiled to JavaScript?
6. Can you mix string and numeric values in an enum?
7. What happens if you pass a number like `404` to a function expecting an `HttpStatus` enum?
8. Why might you avoid using enums in TypeScript?
9. How do you access an enum value by its name and by its value?
10. What is the difference between `enum` and `const enum` in terms of runtime behavior?
