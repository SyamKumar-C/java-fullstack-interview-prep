# Advanced TypeScript Types and Decorators

keyof T produces a union of property keys. typeof value obtains the type of a value.

Mapped types transform properties:
```ts
type Optional<T> = { [K in keyof T]?: T[K] };
```

Conditional types:
```ts
type ElementType<T> = T extends (infer U)[] ? U : T;
```
Conditional types can distribute over unions when the checked type is a naked type parameter.

Decorators attach/transform metadata or behavior on classes and members. Legacy experimental decorators and newer standard decorators differ; know the compiler/framework version used by the project.

**Interview:** What does infer do? It introduces a type variable inside a conditional type pattern.
