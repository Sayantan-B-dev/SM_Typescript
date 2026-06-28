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

## Multi-Extension (Extending Multiple Interfaces)

An interface can extend multiple parent interfaces:

```typescript
interface Nameable {
    name: string;
}

interface Contactable {
    email: string;
    phone?: string;
}

interface Employee extends Nameable, Contactable {
    employeeId: number;
    department: string;
}

let employee: Employee = {
    name: "Bob",
    email: "bob@company.com",
    phone: "555-0100",
    employeeId: 1234,
    department: "Engineering"
};
```

## Extending a Type Alias

Interfaces can also extend type aliases:

```typescript
type Animal = {
    name: string;
    sound(): string;
};

interface Pet extends Animal {
    owner: string;
}

let pet: Pet = {
    name: "Rex",
    sound: () => "Woof!",
    owner: "Alice"
};
```

## Interface Extension with Override

An extending interface can override the type of a parent property with a more specific type:

```typescript
interface Base {
    value: string | number;
}

interface Specific extends Base {
    value: number; // Narrow the type to number only
}

let specific: Specific = {
    value: 42 // OK -- number is assignable to the narrowed type
};

// specific = { value: "hello" }; // Error: Type 'string' is not assignable to type 'number'
```

Note: The overriding type must be assignable to the parent type.

## Declaration Merging (Same-Name Interfaces)

When two interfaces with the same name are declared in the same scope, they merge:

```typescript
interface Car {
    brand: string;
}

interface Car {
    model: string;
    year: number;
}

// After merging, Car has three properties: brand, model, year
let myCar: Car = {
    brand: "Toyota",
    model: "Camry",
    year: 2024
};
```

This is particularly useful for extending types from external libraries or augmenting global types.

## Under the Hood: What Extension Means

Interface extension is a compile-time concept. It does not generate any runtime code.

```typescript
interface Base {
    a: string;
}

interface Extended extends Base {
    b: number;
}

// Extended is equivalent to:
// interface Extended {
//     a: string;
//     b: number;
// }
```

The compiled JavaScript carries no trace of the interface or the extension.

## Practical Example: Hierarchical Interfaces

```typescript
interface Entity {
    id: number;
    createdAt: Date;
    updatedAt: Date;
}

interface Person extends Entity {
    firstName: string;
    lastName: string;
    email: string;
}

interface Customer extends Person {
    ordersCount: number;
    totalSpent: number;
}

interface Employee extends Person {
    employeeId: string;
    salary: number;
    department: string;
}

function sendPromotion(customer: Customer): void {
    console.log(`Sending promotion to ${customer.firstName} at ${customer.email}`);
}

function processPayroll(employee: Employee): void {
    console.log(`Paying ${employee.salary} to ${employee.firstName} ${employee.lastName}`);
}
```

## Analogy

Interface extension is like a family tree. A child inherits traits from both parents. `Employee extends Nameable, Contactable` is like a child who inherits the "name" trait from one parent and the "email" trait from another. Declaration merging is like updating the family record -- new information about the same person is added to their existing entry rather than replacing it.

## Tricky Things to Remember

- An interface can extend multiple interfaces, but multiple inheritance conflicts (same property name with incompatible types) cause errors.
- You can extend a type alias with an interface, but not the reverse (a type alias cannot extend an interface).
- Interface extension only works with object types. You cannot extend primitives or union types.
- When overriding a property type in an extending interface, the new type must be assignable to the original type.
- Declaration merging only works for interfaces, not type aliases.

## Interview Questions

1. How do you extend an interface in TypeScript?
2. Can an interface extend multiple interfaces? How?
3. Can an interface extend a type alias?
4. What is declaration merging and which type construct supports it?
5. What happens if two parent interfaces have a property with the same name but different types?
6. Can you override a property type when extending an interface?
7. Is there any runtime cost to interface extension?
8. How would you model a multi-level type hierarchy using interfaces?
9. What is the difference between extending an interface and using intersection types?
10. Can you extend a union type with an interface?
