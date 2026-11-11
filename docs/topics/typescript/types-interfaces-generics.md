# TypeScript Types, Interfaces and Generics

## 1. Type annotations

TypeScript adds static type checking to JavaScript. Types are checked during development/build and are erased from emitted JavaScript in normal compilation.

```ts
let employeeName: string = 'Asha';
let experience: number = 4.5;
let active: boolean = true;
```

Type inference often makes explicit annotations unnecessary.

## 2. type alias vs interface

Both can describe object shapes. Interfaces support declaration merging and extension. Type aliases can represent unions, intersections, primitives, tuples and mapped/conditional types.

```ts
interface Employee {
  id: number;
  name: string;
}

type EmployeeState = 'ACTIVE' | 'INACTIVE';
type EmployeeWithState = Employee & { state: EmployeeState };
```

Choose based on the shape and team convention; there is no universal rule that one is always better.

## 3. Optional and readonly properties

```ts
interface User {
  readonly id: number;
  name: string;
  email?: string;
}
```

Optional means a property may be absent/undefined depending on compiler settings. readonly prevents assignment through that typed reference; it does not deeply freeze nested objects at runtime.

## 4. Generics

Generics let a function or type preserve information about its input type.

```ts
function first<T>(items: T[]): T | undefined {
  return items[0];
}
const name = first(['Asha', 'Ravi']); // string | undefined
```

Without generics, using any loses the connection between input and output types.

## 5. Generic constraints

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

K must be a valid key of T, and the return type matches that property.

## 6. Classes and access modifiers

TypeScript classes support public, private, protected and readonly modifiers. These are compile-time checks; JavaScript runtime privacy is different. ECMAScript private fields use # syntax.

## 7. Practical scenarios

- Generic ApiResponse<T> for typed HTTP response wrappers.
- Interface for an employee model used across components/services.
- Union type for a finite status field.
- Generic repository helper that preserves entity types.

## 8. Common mistakes

- Using any everywhere.
- Assuming TypeScript interfaces exist at runtime.
- Assuming readonly deeply freezes an object.
- Using a generic when a simple concrete type is clearer.
- Confusing TypeScript private with ECMAScript # private fields.

## 9. Interview questions

**Q1. Type vs interface?** Both describe shapes; type aliases support unions/intersections and more type expressions, while interfaces support declaration merging and extension.

**Q2. What is a generic?** A reusable type parameter that preserves relationships between values and types.

**Q3. What does keyof do?** It produces the union of keys of a type.

**Q4. Does TypeScript prevent invalid runtime API data?** No. Runtime validation is needed at external boundaries.

**Q5. What is type inference?** The compiler derives a type from the value or context.

## 10. Practice

Create a generic ApiResponse<T>, a User interface, and a function that safely reads a property using K extends keyof T.
