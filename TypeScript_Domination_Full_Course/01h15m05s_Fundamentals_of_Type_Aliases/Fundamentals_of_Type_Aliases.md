# Fundamentals of Type Aliases

## Overview

A type alias creates a new name for an existing type. It does not create a new type; it is simply a synonym or shorthand for a type definition. Type aliases are incredibly powerful for simplifying complex type expressions and making code more readable.

## Basic Type Aliases

```typescript
// Alias for a primitive type
type ID = number;
let userId: ID = 12345;

// This is equivalent to:
let userId2: number = 12345;
```

While aliasing primitives may seem useless initially, it becomes valuable when the semantics matter:

```typescript
type UserID = number;
type ProductID = number;
type OrderID = number;

function getUser(id: UserID): void { /* ... */ }
function getProduct(id: ProductID): void { /* ... */ }
function getOrder(id: OrderID): void { /* ... */ }
```

This documents the intent of each parameter even though they are all numbers under the hood.

## Expanded Code Examples

### Example 1: Union Types with Type Aliases

```typescript
// The most common and useful application of type aliases
type Status = "active" | "inactive" | "pending" | "suspended";

function processStatus(status: Status): string {
    switch (status) {
        case "active":
            return "User is active";
        case "inactive":
            return "User is inactive";
        case "pending":
            return "User is pending approval";
        case "suspended":
            return "User is suspended";
        default:
            const exhaustive: never = status;
            return exhaustive;
    }
}

// Complex union types
type NullableString = string | null;
type NumericOrString = number | string;
type Success<T> = { success: true; data: T };
type ErrorResult = { success: false; error: string };
type Result<T> = Success<T> | ErrorResult;

function parseResult<T>(result: Result<T>): T | never {
    if (result.success) {
        return result.data;
    }
    throw new Error(result.error);
}
```

### Example 2: Function Type Aliases

```typescript
// Type alias for a function signature
type Comparator<T> = (a: T, b: T) => number;

const numberComparator: Comparator<number> = (a, b) => a - b;
const stringComparator: Comparator<string> = (a, b) => a.localeCompare(b);

function sortArray<T>(arr: T[], comparator: Comparator<T>): T[] {
    return [...arr].sort(comparator);
}

// More function type aliases
type EventHandler = (event: MouseEvent | KeyboardEvent) => void;
type AsyncCallback<T> = (error: Error | null, result?: T) => void;
type Middleware<T, U> = (input: T, next: (transformed: T) => U) => U;

// Use case: API middleware chain
type RequestHandler = (req: Request, res: Response, next: () => void) => void | Promise<void>;

const authMiddleware: RequestHandler = (req, res, next) => {
    if (req.headers.authorization) {
        next();
    } else {
        res.status(401).send("Unauthorized");
    }
};
```

### Example 3: Generic Type Aliases

```typescript
// Basic generic type alias
type ApiResponse<T> = {
    success: boolean;
    data: T;
    message?: string;
};

// Usage with different types
type UserResponse = ApiResponse<{ id: number; name: string; email: string }>;
type StringResponse = ApiResponse<string>;
type VoidResponse = ApiResponse<void>;

// Multiple type parameters
type Either<L, R> = { type: "left"; value: L } | { type: "right"; value: R };

function processEither<L, R>(either: Either<L, R>): void {
    if (either.type === "left") {
        console.log("Left:", either.value);
    } else {
        console.log("Right:", either.value);
    }
}

// Generic constraint
type HasId<T extends { id: number }> = T & { readonly id: number };

interface User {
    id: number;
    name: string;
}

type SafeUser = HasId<User>; // { readonly id: number; name: string; }

// Recursive generic type
type TreeNode<T> = {
    value: T;
    children?: TreeNode<T>[];
};

const tree: TreeNode<number> = {
    value: 1,
    children: [
        { value: 2, children: [{ value: 4 }, { value: 5 }] },
        { value: 3 }
    ]
};
```

### Example 4: Mapped Type Aliases

```typescript
// Make all properties readonly
type Readonly<T> = {
    readonly [K in keyof T]: T[K];
};

// Make all properties optional
type Optional<T> = {
    [K in keyof T]?: T[K];
};

// Make all properties required (remove optional)
type Required<T> = {
    [K in keyof T]-?: T[K];
};

// Make all properties nullable
type Nullable<T> = {
    [K in keyof T]: T[K] | null;
};

// Pick specific properties
type PickProperties<T, K extends keyof T> = {
    [P in K]: T[P];
};

// Omit specific properties
type OmitProperties<T, K extends keyof T> = {
    [P in Exclude<keyof T, K>]: T[P];
};

// Usage
interface User {
    name: string;
    age: number;
    email: string;
    password: string;
}

type PublicUser = OmitProperties<User, "password">;
// { name: string; age: number; email: string; }

type PartialUser = Optional<User>;
// { name?: string; age?: number; email?: string; password?: string; }

type ReadonlyUser = Readonly<User>;
// { readonly name: string; readonly age: number; ... }
```

### Example 5: Template Literal Type Aliases

```typescript
// Template literal types (TypeScript 4.1+)
type EventName = `on${Capitalize<string>}`;
// Matches: "onClick", "onChange", "onSubmit", etc.

type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type APIEndpoint = `/${string}`;

type ApiRoute = `${HTTPMethod} ${APIEndpoint}`;
// "GET /users", "POST /users/create", etc.

// Conditional template literals
type Brand<T, B extends string> = T & { __brand: B };
type UserId = Brand<number, "UserId">;
type ProductId = Brand<number, "ProductId">;

// String manipulation types
type UppercaseKeys<T> = {
    [K in keyof T as Uppercase<string & K>]: T[K];
};

interface User {
    name: string;
    email: string;
}

type UppercaseUser = UppercaseKeys<User>;
// { NAME: string; EMAIL: string; }

// Extracting route parameters
type ExtractParams<T extends string> =
    T extends `${string}:${infer Param}/${infer Rest}`
        ? Param | ExtractParams<Rest>
        : T extends `${string}:${infer Param}`
            ? Param
            : never;

type UserRouteParams = ExtractParams<"/users/:userId/posts/:postId">;
// "userId" | "postId"
```

### Example 6: Conditional Type Aliases

```typescript
// Basic conditional type
type IsString<T> = T extends string ? "yes" : "no";

type Test1 = IsString<string>;  // "yes"
type Test2 = IsString<number>;  // "no"
type Test3 = IsString<string | number>; // "yes" | "no" (distributed)

// Filter types from a union
type ExtractType<T, U> = T extends U ? T : never;

type NumbersAndStrings = string | number | boolean;
type OnlyStrings = ExtractType<NumbersAndStrings, string>;
// string

type OnlyNumbers = ExtractType<NumbersAndStrings, number>;
// number

// Exclude types from a union
type ExcludeType<T, U> = T extends U ? never : T;

type WithoutStrings = ExcludeType<NumbersAndStrings, string>;
// number | boolean

// Return type of a function
type ReturnType<T> = T extends (...args: unknown[]) => infer R ? R : never;

type Fn = (x: number) => string;
type FnReturn = ReturnType<Fn>; // string

// Deep readonly with conditional types
type DeepReadonly<T> = {
    readonly [K in keyof T]: T[K] extends object
        ? DeepReadonly<T[K]>
        : T[K];
};

interface Config {
    server: {
        host: string;
        port: number;
    };
    debug: boolean;
}

type ReadonlyConfig = DeepReadonly<Config>;
// { readonly server: { readonly host: string; readonly port: number; }; readonly debug: boolean; }
```

### Example 7: Type Aliases with Intersection

```typescript
// Combining multiple type aliases
type WithTimestamp = {
    createdAt: Date;
    updatedAt: Date;
};

type WithMetadata = {
    version: number;
    author: string;
};

type WithAudit = {
    createdBy: string;
    lastModifiedBy: string;
};

// Intersection combines all properties
type AuditedEntity = WithTimestamp & WithMetadata & WithAudit & {
    id: number;
};

let entity: AuditedEntity = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    version: 1,
    author: "Alice",
    createdBy: "system",
    lastModifiedBy: "Alice"
};

// Intersection with generics
type WithId<T> = T & { id: number };
type WithDates<T> = T & WithTimestamp;

type Product = WithId<WithDates<{
    name: string;
    price: number;
}>>;

const product: Product = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    name: "Laptop",
    price: 999.99
};
```

### Example 8: Type Alias for Discriminated Unions

```typescript
// Discriminated union with type aliases
type Shape =
    | { kind: "circle"; radius: number }
    | { kind: "square"; side: number }
    | { kind: "rectangle"; width: number; height: number }
    | { kind: "triangle"; base: number; height: number };

function area(shape: Shape): number {
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.side ** 2;
        case "rectangle":
            return shape.width * shape.height;
        case "triangle":
            return 0.5 * shape.base * shape.height;
        default:
            const _exhaustive: never = shape;
            return _exhaustive;
    }
}

// State machine with discriminated unions
type RequestState<T> =
    | { status: "idle" }
    | { status: "loading" }
    | { status: "success"; data: T }
    | { status: "error"; error: string };

function renderState<T>(state: RequestState<T>): string {
    switch (state.status) {
        case "idle":
            return "Ready to fetch";
        case "loading":
            return "Loading...";
        case "success":
            return `Data: ${JSON.stringify(state.data)}`;
        case "error":
            return `Error: ${state.error}`;
    }
}
```

### Example 9: Type Alias for Utility Functions

```typescript
// Deep partial -- makes all nested properties optional
type DeepPartial<T> = T extends object
    ? { [K in keyof T]?: DeepPartial<T[K]> }
    : T;

interface NestedConfig {
    server: {
        host: string;
        port: number;
        auth: {
            username: string;
            password: string;
        };
    };
    database: {
        url: string;
        pool: {
            min: number;
            max: number;
        };
    };
}

function updateConfig(config: DeepPartial<NestedConfig>): void {
    // All properties are optional, even deeply nested ones
    if (config.server?.auth?.username) {
        console.log(`Updating username: ${config.server.auth.username}`);
    }
}

// Non-nullable type
type NonNullable<T> = T extends null | undefined ? never : T;
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>; // string

// Parameters type
type Parameters<T extends (...args: unknown[]) => unknown> =
    T extends (...args: infer P) => unknown ? P : never;

type Fn = (name: string, age: number) => boolean;
type FnParams = Parameters<Fn>; // [string, number]
```

### Example 10: Practical Domain Modeling

```typescript
// Domain types using type aliases
type Email = string & { __brand: "Email" };
type PhoneNumber = string & { __brand: "Phone" };
type PostalCode = string & { __brand: "PostalCode" };

function createEmail(value: string): Email | null {
    return value.includes("@") ? (value as Email) : null;
}

function sendEmail(to: Email, subject: string, body: string): void {
    console.log(`Sending email to ${to}: ${subject}`);
}

// Usage
const maybeEmail = createEmail("alice@example.com");
if (maybeEmail) {
    sendEmail(maybeEmail, "Hello", "How are you?");
    // sendEmail("not-email", "Hello", "Body"); // Error -- string not assignable to Email
}

// Complex domain type
type OrderStatus =
    | { type: "pending"; createdAt: Date }
    | { type: "confirmed"; confirmedAt: Date; paymentMethod: string }
    | { type: "shipped"; shippedAt: Date; trackingNumber: string }
    | { type: "delivered"; deliveredAt: Date; signedBy: string }
    | { type: "cancelled"; cancelledAt: Date; reason: string };

type Order = {
    id: string;
    customerId: string;
    items: Array<{ productId: string; quantity: number; price: number }>;
    total: number;
    status: OrderStatus;
    shippingAddress: {
        street: string;
        city: string;
        state: string;
        zip: PostalCode;
    };
};

function getOrderSummary(order: Order): string {
    const statusText = (() => {
        switch (order.status.type) {
            case "pending": return "Pending";
            case "confirmed": return `Confirmed (${order.status.paymentMethod})`;
            case "shipped": return `Shipped (Tracking: ${order.status.trackingNumber})`;
            case "delivered": return `Delivered (Signed by: ${order.status.signedBy})`;
            case "cancelled": return `Cancelled: ${order.status.reason}`;
        }
    })();

    return `Order ${order.id}: ${order.items.length} items, $${order.total} - ${statusText}`;
}
```

## Type Aliases vs Interfaces Summary

| Aspect              | Type Alias                              | Interface                             |
|---------------------|-----------------------------------------|---------------------------------------|
| Primitives          | Yes (`type ID = number`)                | No                                    |
| Union types         | Yes (`type Status = "on" \| "off"`)    | No                                    |
| Intersection        | Using `&`                               | Using `extends`                       |
| Declaration merging | Not supported                           | Supported                             |
| Extends             | Via intersection (`&`)                  | Via `extends` keyword                 |
| Computed/mapped     | Yes                                     | No                                    |

## Analogy

A type alias is like giving a nickname to a phone number. Instead of saying "call 555-1234," you say "call Home." The nickname does not change the number itself; it just provides a convenient, memorable name for it. Similarly, `type ID = number` does not create a new type -- it creates a more descriptive name for the existing `number` type.

## Tricky Things to Remember

- A type alias is NOT a new type -- it is just an alias. `type ID = number` means `ID` and `number` are interchangeable.
- You cannot use a type alias in `implements` or `extends` clauses the same way you use an interface. However, you can use intersection types to simulate extension.
- Type aliases can reference themselves recursively (e.g., for tree structures), but must be careful to avoid infinite recursion.
- Type aliases can use computed property names and template literal types, which interfaces cannot.
- Type aliases cannot be re-opened or merged -- redeclaring the same type alias name causes an error.

## More Tricky Scenarios

### Tricky 1: Type Alias Not a New Type

```typescript
type UserID = number;
type ProductID = number;

let userId: UserID = 1;
let productId: ProductID = 2;

// These are interchangeable:
userId = productId; // OK -- they are both just 'number'
productId = userId; // OK

// If you need truly distinct types, use branding:
type BrandedID<T, B> = T & { __brand: B };
type DistinctUserID = BrandedID<number, "User">;
type DistinctProductID = BrandedID<number, "Product">;
```

### Tricky 2: Recursive Type Aliases

```typescript
// JSON value type -- recursive type alias
type JSONValue =
    | string
    | number
    | boolean
    | null
    | JSONValue[]
    | { [key: string]: JSONValue };

const data: JSONValue = {
    name: "Alice",
    age: 30,
    hobbies: ["reading", "coding"],
    address: {
        street: "123 Main St",
        city: "NYC",
        coordinates: [40.7128, -74.006]
    }
};
```

### Tricky 3: Type Alias Doesn't Infer in Error Messages

```typescript
type MyComplexType = {
    a: string;
    b: number;
    c: boolean;
    d: {
        e: string[];
        f: Date;
    };
};

// When there's an error, TypeScript may expand the alias:
// function process(data: MyComplexType) { ... }
// Error message shows the expanded type, not "MyComplexType"
// This can make error messages harder to read
```

## Interview Questions with Answers

### 1. What is a type alias and how do you create one?

A type alias creates a new name for an existing type using the `type` keyword: `type MyType = ExistingType;`. For example, `type ID = number;` creates an alias `ID` for the `number` type. Type aliases can represent primitives, unions, intersections, tuples, objects, functions, and complex generic types. They are purely compile-time constructs and are erased during compilation.

### 2. Can a type alias represent a union type? Give an example.

Yes, this is one of the most powerful uses of type aliases. Example: `type Status = "active" | "inactive" | "pending";`. This creates a reusable type that can only be one of the specified string literals. Union types can also combine other types: `type Result = string | number | boolean;` or `type Nullable<T> = T | null;`.

### 3. How do you create a generic type alias?

Generic type aliases use type parameters in angle brackets: `type ApiResponse<T> = { success: boolean; data: T; message?: string; };`. You can have multiple parameters: `type Either<L, R> = { type: "left"; value: L } | { type: "right"; value: R };`. Generic constraints use `extends`: `type HasId<T extends { id: number }> = T & { readonly id: number };`.

### 4. What is the difference between a type alias and an interface?

Type aliases can represent primitives, unions, tuples, and objects; interfaces can only represent objects. Type aliases support mapped and computed types; interfaces do not. Interfaces support declaration merging; type aliases do not. For object types, interfaces can be `extends`ed and `implements`ed; type aliases use intersection (`&`) for extension. Interfaces are generally preferred for object shapes; type aliases are preferred for unions, primitives, and complex type computations.

### 5. Can a type alias extend another type? How?

Type aliases do not use `extends`, but you can achieve the same effect using intersection types: `type Admin = User & { role: string; permissions: string[]; };`. This creates a new type that combines all properties from `User` with the additional properties. For multiple "parents": `type C = A & B & { customProp: number; };`. This is functionally similar to `interface C extends A, B { customProp: number; }`.

### 6. Why would you alias a primitive type like `type ID = number`?

Aliasing primitives provides semantic meaning and self-documentation. `function getUser(id: UserID): void` is more descriptive than `function getUser(id: number): void`. It also makes future changes easier: if you need to change `UserID` from `number` to `string`, you change the alias in one place. Additionally, it prevents accidental mixing of semantically different values that happen to share the same base type.

### 7. How do you use a type alias to define a function signature?

Use the arrow function syntax: `type Comparator<T> = (a: T, b: T) => number;`. Then variables can be typed with this alias: `const numberComp: Comparator<number> = (a, b) => a - b;`. Function type aliases are useful for callback signatures, event handlers, middleware functions, and any reusable function contract.

### 8. Can type aliases be recursive?

Yes, type aliases can reference themselves recursively. Common examples include tree structures: `type TreeNode<T> = { value: T; children?: TreeNode<T>[]; };` and JSON values: `type JSONValue = string | number | boolean | null | JSONValue[] | { [key: string]: JSONValue; };`. TypeScript checks for infinite recursion and will report an error if the recursion cannot be resolved.

### 9. What is declaration merging and why does it not work with type aliases?

Declaration merging allows multiple declarations of the same name to be automatically combined. It only works with interfaces, not type aliases. If you declare `type User = { name: string; }` and then `type User = { age: number; }`, TypeScript reports a duplicate identifier error. This is because interfaces are declarative (open for extension), while type aliases are definitional (closed after creation).

### 10. How do you use type aliases with mapped and conditional types?

Mapped types transform properties: `type Readonly<T> = { readonly [K in keyof T]: T[K]; }`. Conditional types select types based on conditions: `type IsString<T> = T extends string ? "yes" : "no";`. Combined, they enable powerful patterns: `type DeepReadonly<T> = { readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K]; }`. These are used to create utility types like `Partial<T>`, `Required<T>`, `Pick<T, K>`, `Omit<T, K>`, and more.
