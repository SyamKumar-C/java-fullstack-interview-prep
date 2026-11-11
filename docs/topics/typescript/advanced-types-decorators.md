# TypeScript Advanced Types and Decorators

## 1. Union and intersection types

A union means a value can be one of several types. An intersection combines requirements from multiple types.

```ts
type Id = string | number;
type Timestamped = { createdAt: Date };
type User = { id: number; name: string } & Timestamped;
```

## 2. Literal types and discriminated unions

Literal types restrict values. A discriminated union uses a shared literal property so TypeScript can narrow the type safely.

```ts
type Result =
  | { status: 'success'; data: string }
  | { status: 'error'; message: string };

function show(result: Result) {
  if (result.status === 'success') {
    return result.data;
  }
  return result.message;
}
```

## 3. keyof, typeof and indexed access

- keyof T: union of property keys.
- typeof value: type of a value.
- T[K]: type of property K in T.

```ts
type User = { id: number; name: string };
type UserKey = keyof User; // 'id' | 'name'
type UserName = User['name']; // string
```

## 4. Mapped and conditional types

Mapped types transform each property in a type. Conditional types choose a type based on assignability.

```ts
type ReadonlyCopy<T> = { readonly [K in keyof T]: T[K] };
type ElementType<T> = T extends (infer U)[] ? U : T;
```

Built-ins include Partial, Required, Readonly, Pick, Omit, Record, Exclude, Extract, NonNullable and ReturnType.

## 5. Generics and constraints

Generics preserve relationships between input and output types. Constraints restrict allowed types.

```ts
function getId<T extends { id: number }>(value: T): number {
  return value.id;
}
```

## 6. Decorators

Decorators are metadata/behavior hooks for classes and class members depending on the TypeScript decorator model and compiler configuration. Angular uses decorators such as @Component and @Injectable.

TypeScript's standard decorator support and the older experimental decorator implementation differ. Do not assume every legacy parameter/property decorator behaves the same under the newer model. Follow the framework and tsconfig version in use.

## 7. Practical scenarios

- Discriminated union for API loading/success/error state.
- Pick/Omit to define safe DTO shapes.
- Record to model a lookup by a known key union.
- Generic API wrapper to preserve response types.
- Angular decorators to declare component and dependency metadata.

## 8. Common mistakes

- Using any and losing type checking.
- Overusing complex conditional types that are hard to maintain.
- Assuming a type assertion validates runtime data.
- Treating interfaces/types as runtime objects; TypeScript types are erased.
- Mixing decorator models without checking compiler settings.

## 9. Interview questions

**Q1. Union vs intersection?** Union accepts one of the listed types; intersection requires all combined members.

**Q2. What is a discriminated union?** A union whose variants share a literal field used for safe narrowing.

**Q3. keyof?** Produces a union of an object's property keys.

**Q4. Are TypeScript types available at runtime?** No, they are erased during compilation.

**Q5. What is a generic constraint?** A requirement such as T extends SomeType that limits valid type arguments.

## 10. Practice

Model an API response with loading, success and error variants. Use a discriminated union and write a function that handles each state without any.
