# Extending Interfaces

## Overview

Interface extension allows you to create new interfaces that inherit properties from one or more existing interfaces. This promotes code reuse and establishes type hierarchies.

## Basic Extension

```typescript
interface User {
    name: string;
    email: string;
}

interface Admin extends User {
    role: "admin";
    permissions: string[];
}
```

When you use `Admin`, it requires all properties from `User` plus its own:

```typescript
function manageUser(admin: Admin): void {
    // admin has: name, email, role, permissions
    console.log(`${admin.name} (${admin.role}) can: ${admin.permissions.join(", ")}`);
}

// Must include ALL properties from User AND Admin
manageUser({
    name: "Alice",
    email: "alice@example.com",
    role: "admin",
    permissions: ["read", "write", "delete"]
});
```

## Expanded Code Examples

### Example 1: Multi-Extension (Extending Multiple Interfaces)

```typescript
interface Nameable {
    name: string;
}

interface Contactable {
    email: string;
    phone?: string;
}

interface Addressable {
    address: string;
    city: string;
    zipCode: string;
}

interface Employee extends Nameable, Contactable, Addressable {
    employeeId: number;
    department: string;
    salary: number;
}

let employee: Employee = {
    name: "Bob Smith",
    email: "bob@company.com",
    phone: "555-0100",
    address: "123 Main St",
    city: "New York",
    zipCode: "10001",
    employeeId: 1234,
    department: "Engineering",
    salary: 85000
};
```

### Example 2: Extending a Type Alias

```typescript
type Animal = {
    name: string;
    sound(): string;
};

interface Pet extends Animal {
    owner: string;
    vetName: string;
}

class Dog implements Pet {
    name: string = "Rex";
    owner: string = "Alice";
    vetName: string = "Dr. Brown";

    sound(): string {
        return "Woof! Woof!";
    }
}

let dog: Pet = new Dog();
console.log(`${dog.name} says ${dog.sound()}`);

// You can also merge with interfaces
interface WildAnimal extends Animal {
    habitat: string;
    isDangerous: boolean;
}

let lion: WildAnimal = {
    name: "Simba",
    habitat: "Savannah",
    isDangerous: true,
    sound: () => "Roar!"
};
```

### Example 3: Interface Extension with Property Override

```typescript
interface Base {
    value: string | number;
    id: number;
}

interface Specific extends Base {
    value: number;  // Narrowing from string | number to just number
    name: string;   // New property
}

let specific: Specific = {
    value: 42,       // OK -- number is assignable to the parent type
    id: 1,
    name: "specific item"
};

// specific = { value: "hello", id: 1, name: "test" };
// Error: 'value' is typed as number in Specific, but "hello" is string

// The parent type property must be assignable to the override type
interface NumericBase {
    count: number;
}

// interface BadSpecific extends NumericBase {
//     count: string; // Error: string not assignable to number
// }
```

### Example 4: Deep Inheritance Chain

```typescript
interface Entity {
    id: number;
    createdAt: Date;
    updatedAt: Date;
}

interface Person extends Entity {
    firstName: string;
    lastName: string;
    dateOfBirth: Date;
}

interface Customer extends Person {
    email: string;
    ordersCount: number;
    totalSpent: number;
    loyaltyPoints: number;
}

interface PremiumCustomer extends Customer {
    membershipTier: "gold" | "platinum" | "diamond";
    personalAgent: string;
    prioritySupport: boolean;
}

// PremiumCustomer has ALL properties from:
// Entity (id, createdAt, updatedAt)
// Person (firstName, lastName, dateOfBirth)
// Customer (email, ordersCount, totalSpent, loyaltyPoints)
// PremiumCustomer (membershipTier, personalAgent, prioritySupport)

function sendPremiumOffer(customer: PremiumCustomer): void {
    const fullName = `${customer.firstName} ${customer.lastName}`;
    const since = customer.createdAt.getFullYear();
    console.log(`Sending premium offer to ${fullName} (member since ${since})`);
    console.log(`Current loyalty points: ${customer.loyaltyPoints}`);
    console.log(`Membership tier: ${customer.membershipTier}`);
}
```

### Example 5: Declaration Merging in Detail

```typescript
// Declaration merging allows adding properties to existing interfaces
// This is commonly used to extend globally available types

// Augmenting the Window interface
interface Window {
    appVersion: string;
    analytics: {
        trackEvent(event: string, data?: Record<string, unknown>): void;
    };
}

// Now Window has the standard properties PLUS appVersion and analytics
window.appVersion = "1.0.0";
window.analytics.trackEvent("page_view", { page: "/home" });

// Augmenting Array
interface Array<T> {
    first(): T | undefined;
    last(): T | undefined;
}

// Now all arrays have first() and last()
const numbers = [1, 2, 3, 4, 5];
console.log(numbers.first()); // 1
console.log(numbers.last());  // 5

// Implementation (in a real project, you'd add these to the prototype)
Array.prototype.first = function <T>(this: T[]): T | undefined {
    return this[0];
};

Array.prototype.last = function <T>(this: T[]): T | undefined {
    return this[this.length - 1];
};
```

### Example 6: Extending Interfaces with Generics

```typescript
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
    timestamp: Date;
}

interface PaginatedResponse<T> extends ApiResponse<T[]> {
    total: number;
    page: number;
    pageSize: number;
    totalPages: number;
}

interface User {
    id: number;
    name: string;
    email: string;
}

// Type-safe paginated API response
type UserListResponse = PaginatedResponse<User>;

const response: UserListResponse = {
    data: [
        { id: 1, name: "Alice", email: "alice@test.com" },
        { id: 2, name: "Bob", email: "bob@test.com" }
    ],
    status: 200,
    message: "Users retrieved successfully",
    timestamp: new Date(),
    total: 2,
    page: 1,
    pageSize: 10,
    totalPages: 1
};

// Generic constraint with extends
interface Identifiable {
    id: number;
}

interface Repository<T extends Identifiable> {
    getById(id: number): T | undefined;
    save(item: T): void;
    delete(id: number): void;
}
```

### Example 7: Recursive Interface Extension

```typescript
interface TreeNode<T> {
    value: T;
    children?: TreeNode<T>[];
}

interface ExtendedTreeNode<T> extends TreeNode<T> {
    parent?: ExtendedTreeNode<T>;
    depth: number;
    isLeaf: boolean;
    addChild(value: T): ExtendedTreeNode<T>;
}

class Tree<T> implements ExtendedTreeNode<T> {
    value: T;
    children?: ExtendedTreeNode<T>[];
    parent?: ExtendedTreeNode<T>;
    depth: number = 0;
    isLeaf: boolean = true;

    constructor(value: T, parent?: ExtendedTreeNode<T>) {
        this.value = value;
        this.parent = parent;
        if (parent) {
            this.depth = parent.depth + 1;
        }
        this.children = [];
    }

    addChild(value: T): ExtendedTreeNode<T> {
        const child = new Tree(value, this);
        this.children!.push(child);
        this.isLeaf = false;
        return child;
    }
}
```

### Example 8: Extending Third-Party Interfaces

```typescript
// Suppose you have a library with this interface:
interface RequestOptions {
    url: string;
    method: "GET" | "POST";
    headers?: Record<string, string>;
}

// Your application needs to extend it:
interface AppRequestOptions extends RequestOptions {
    retryCount?: number;
    timeout?: number;
    cacheResponse?: boolean;
    onProgress?: (percent: number) => void;
}

function makeRequest(options: AppRequestOptions): Promise<Response> {
    const { url, method, headers, timeout = 5000, retryCount = 0 } = options;
    console.log(`Making ${method} request to ${url} with timeout ${timeout}ms`);
    // Implementation...
    return fetch(url, { method, headers });
}

// Extending library interfaces via declaration merging
// If the library declares an interface, you can augment it:
// declare module "some-library" {
//     interface RequestOptions {
//         retryCount?: number;
//     }
// }
```

### Example 9: Conditional Extension (Pick, Omit, Partial)

```typescript
interface FullUser {
    id: number;
    name: string;
    email: string;
    password: string;
    age: number;
    address: string;
    phone: string;
    role: "admin" | "user";
    createdAt: Date;
    updatedAt: Date;
}

// Create a public-facing interface that omits sensitive fields
interface PublicUser extends Omit<FullUser, "password" | "createdAt" | "updatedAt"> {
    readonly memberSince: number; // New computed property
}

const publicUser: PublicUser = {
    id: 1,
    name: "Alice",
    email: "alice@test.com",
    age: 30,
    address: "123 Main St",
    phone: "555-0100",
    role: "user",
    memberSince: 2024
};
// No password field -- Omit removed it

// Partial update interface
interface UpdateUser extends Partial<Omit<FullUser, "id" | "createdAt">> {
    updatedAt: Date; // Always required
}

const updates: UpdateUser = {
    name: "Alice Smith", // Only updating name
    updatedAt: new Date()
};
```

### Example 10: Interface Extension with Factory Pattern

```typescript
interface Vehicle {
    make: string;
    model: string;
    year: number;
    start(): string;
    stop(): string;
}

interface ElectricVehicle extends Vehicle {
    batteryCapacity: number;    // kWh
    range: number;              // miles
    charge(): string;
}

interface GasVehicle extends Vehicle {
    fuelCapacity: number;       // gallons
    mpg: number;
    refuel(): string;
}

interface HybridVehicle extends ElectricVehicle, GasVehicle {
    electricMode: boolean;
    switchMode(): string;
}

// Factory function returning extended interfaces
function createVehicle(type: "electric"): ElectricVehicle;
function createVehicle(type: "gas"): GasVehicle;
function createVehicle(type: "hybrid"): HybridVehicle;
function createVehicle(type: "electric" | "gas" | "hybrid"): Vehicle {
    switch (type) {
        case "electric":
            return {
                make: "Tesla",
                model: "Model 3",
                year: 2024,
                batteryCapacity: 75,
                range: 340,
                start: () => "Motor hums silently",
                stop: () => "Regenerative braking engaged",
                charge: () => "Charging at 250 kW"
            };
        case "gas":
            return {
                make: "Toyota",
                model: "Camry",
                year: 2024,
                fuelCapacity: 15,
                mpg: 32,
                start: () => "Engine roars to life",
                stop: () => "Engine turns off",
                refuel: () => "Filling up with premium"
            };
        case "hybrid":
            return {
                make: "Toyota",
                model: "Prius",
                year: 2024,
                batteryCapacity: 8.8,
                range: 640,
                fuelCapacity: 11.4,
                mpg: 52,
                electricMode: true,
                start: () => "Electric motor engages",
                stop: () => "Engine shuts off automatically",
                charge: () => "Regenerative braking charging battery",
                refuel: () => "Filling up with regular",
                switchMode: () => "Switching to gas engine"
            };
    }
}

const myCar = createVehicle("hybrid");
console.log(myCar.start());
console.log(myCar.switchMode());
console.log(myCar.refuel());
```

## Analogy

Interface extension is like a family tree. A child inherits traits from both parents. `Employee extends Nameable, Contactable` is like a child who inherits the "name" trait from one parent and the "email" trait from another. Declaration merging is like updating the family record -- new information about the same person is added to their existing entry rather than replacing it.

## Tricky Things to Remember

- An interface can extend multiple interfaces, but multiple inheritance conflicts (same property name with incompatible types) cause errors.
- You can extend a type alias with an interface, but not the reverse (a type alias cannot extend an interface).
- Interface extension only works with object types. You cannot extend primitives or union types.
- When overriding a property type in an extending interface, the new type must be assignable to the original type.
- Declaration merging only works for interfaces, not type aliases.

## More Tricky Scenarios

### Tricky 1: Diamond Problem in Interface Extension

```typescript
interface A {
    value: string;
}

interface B {
    value: number;
}

// This will cause an error:
// interface C extends A, B {} // Error: value has incompatible types
// 'value' is string in A and number in B -- cannot resolve conflict

// Fix: explicitly resolve in the extending interface
interface C extends A, B {
    value: string | number;
}
```

### Tricky 2: Merged Interface with Extends

```typescript
// extends and declaration merging interact
interface Base {
    id: number;
}

interface Derived extends Base {
    name: string;
}

// Separate declaration -- merges with the existing Derived
interface Derived {
    email: string;
}

// Result: Derived has id (from Base), name, and email
const item: Derived = {
    id: 1,
    name: "Alice",
    email: "alice@test.com"
};
```

### Tricky 3: readonly and Extends

```typescript
interface Mutable {
    value: string;
}

interface ReadonlyOverride extends Mutable {
    readonly value: string; // Can narrow from mutable to readonly
}

let mutable: Mutable = { value: "hello" };
mutable.value = "world"; // OK

let readonly: ReadonlyOverride = { value: "fixed" };
// readonly.value = "changed"; // Error: readonly
```

## Interview Questions with Answers

### 1. How do you extend an interface in TypeScript?

Use the `extends` keyword: `interface Admin extends User { role: string; }`. The new interface inherits all properties and methods from the parent interface and can add new ones. You can extend multiple interfaces by separating them with commas: `interface Employee extends User, Timestamped { ... }`.

### 2. Can an interface extend multiple interfaces? How?

Yes, separate parent interfaces with commas: `interface Employee extends Nameable, Contactable, Addressable { employeeId: number; }`. The extending interface will contain all properties from all parent interfaces. However, if two parents have the same property name with incompatible types, you will get a compile error.

### 3. Can an interface extend a type alias?

Yes, an interface can extend a type alias as long as the type alias represents an object type. For example: `type Animal = { name: string; sound(): string; }; interface Pet extends Animal { owner: string; }`. However, a type alias cannot extend an interface using `extends`. The reverse direction (interface extending type alias) works, but type extending interface does not.

### 4. What is declaration merging and which type construct supports it?

Declaration merging is a feature where multiple declarations of the same interface name are automatically combined into a single interface. Only interfaces support declaration merging; type aliases with the same name cause a compile error. This is useful for augmenting types from external libraries or adding properties to global types like `Window` or `Array`.

### 5. What happens if two parent interfaces have a property with the same name but different types?

This creates a conflict that TypeScript reports as a compile error. For example, if interface A has `value: string` and interface B has `value: number`, then `interface C extends A, B { }` will error. You must either rename the conflicting properties or explicitly override the property in the extending interface with a compatible type.

### 6. Can you override a property type when extending an interface?

Yes, but the overriding type must be assignable to (compatible with) the parent type. You can narrow a type (e.g., from `string | number` to `number`) or make it more specific (e.g., from `string` to `"hello" | "world"`). You cannot override with an incompatible type (e.g., overriding `number` with `string`). Readonly can also be added to a property that was mutable in the parent.

### 7. Is there any runtime cost to interface extension?

No, interfaces are a compile-time-only feature in TypeScript. They are completely removed during compilation, and no JavaScript code is generated for interfaces or their extensions. There is zero runtime cost. The extension relationship is purely a type-level concept that exists only during type checking.

### 8. How would you model a multi-level type hierarchy using interfaces?

Use a chain of extensions: `interface Entity { id: number; }`, `interface Person extends Entity { name: string; }`, `interface Customer extends Person { email: string; }`, `interface PremiumCustomer extends Customer { tier: string; }`. Each level adds more specific properties. This creates a clear hierarchy where each type inherits all properties from its ancestors.

### 9. What is the difference between extending an interface and using intersection types?

Interface extension (`interface B extends A {}`) creates a new named interface with defined inheritance. Intersection types (`type B = A & { ... }`) combine types using `&`. Key differences: interfaces support declaration merging, intersections do not; interfaces can be `implements`ed by classes, intersections of types cannot be used directly in `implements`; conflicting properties cause errors in extends, but produce `never` in intersections; extends is more readable for object hierarchies.

### 10. Can you extend a union type with an interface?

No, you cannot extend a union type with an interface. The `extends` keyword in interfaces requires a statically known object type. Union types represent one-of-many possibilities and do not have a fixed set of properties that can be extended. If you need to extend a union type, consider using intersection types (`type Extended = UnionType & { additionalProp: string; }`), but this may produce unexpected results if the union members have conflicting structures.
