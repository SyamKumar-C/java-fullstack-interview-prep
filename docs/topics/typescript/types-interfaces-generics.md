# TypeScript Types, Interfaces and Generics

Both type aliases and interfaces describe object shapes. Interfaces support declaration merging and extension; type aliases can represent unions, intersections, primitives, tuples and mapped types.

```ts
interface User { id: number; name: string }
type Status = 'NEW' | 'PAID' | 'CANCELLED';
```

Union A | B means either shape; intersection A & B combines requirements.

Generics preserve relationships between input and output types:
```ts
function identity<T>(value: T): T { return value; }
const result = identity<number>(10);
```

**Interview:** interface vs type? Interfaces are extendable object contracts and merge; type aliases are more flexible for unions and other type expressions.
