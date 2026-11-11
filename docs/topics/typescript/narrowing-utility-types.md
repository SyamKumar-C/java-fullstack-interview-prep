# Type Narrowing and Utility Types

Narrowing uses typeof, instanceof, in, equality checks, discriminated unions and user-defined type guards.

```ts
type Result = { ok: true; data: string } | { ok: false; error: string };
function show(result: Result) {
  if (result.ok) return result.data;
  return result.error;
}
```

A predicate such as `value is User` tells the compiler the type after a successful check; implementation must validate that claim.

Know Partial, Required, Readonly, Pick, Omit, Record, Exclude, Extract, NonNullable, ReturnType, Parameters and Awaited.

```ts
type UserUpdate = Partial<Pick<User, 'name'>>;
```

**Interview:** any vs unknown? any disables checking; unknown requires narrowing before use.
