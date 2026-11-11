# TypeScript Type Narrowing and Utility Types

## 1. Why narrowing?

A union type may represent several possible values. Narrowing uses runtime checks so TypeScript can safely determine which member is present.

## 2. Common narrowing techniques

- typeof: primitive checks.
- instanceof: class instance checks.
- in: property existence.
- Equality checks: compare literal values.
- Truthiness: narrow null/undefined and falsy values carefully.
- User-defined type guards: functions returning `value is Type`.

```ts
function format(value: string | number) {
  if (typeof value === 'string') {
    return value.toUpperCase();
  }
  return value.toFixed(2);
}
```

## 3. Type predicates

```ts
interface User { id: number; name: string }

function isUser(value: unknown): value is User {
  return typeof value === 'object'
    && value !== null
    && 'id' in value
    && typeof (value as any).id === 'number'
    && 'name' in value
    && typeof (value as any).name === 'string';
}
```

For untrusted API data, validate every required field or use a schema-validation library. A type assertion alone does not validate data.

## 4. Utility types

| Utility | Purpose |
|---|---|
| Partial<T> | Make properties optional |
| Required<T> | Make properties required |
| Readonly<T> | Make properties readonly |
| Pick<T, K> | Select properties |
| Omit<T, K> | Remove properties |
| Record<K, V> | Map keys to a value type |
| Exclude<T, U> | Remove union members assignable to U |
| Extract<T, U> | Keep union members assignable to U |
| NonNullable<T> | Remove null and undefined |
| ReturnType<F> | Get function return type |

## 5. Example

```ts
type User = { id: number; name: string; email: string };
type UserSummary = Pick<User, 'id' | 'name'>;
type UserUpdate = Partial<Omit<User, 'id'>>;
```

## 6. unknown vs any

unknown accepts any value but requires narrowing before use. any disables much of TypeScript's checking. Prefer unknown for values from JSON, local storage or external boundaries.

## 7. Practical scenarios

- API response starts as unknown and is validated.
- Form update accepts Partial<User> but excludes immutable ID.
- A state union is narrowed by status.
- A function accepts string | number and branches safely.

## 8. Common mistakes

- Using any instead of unknown.
- Assuming a type assertion performs validation.
- Forgetting null checks before property access.
- Creating overly broad type guards that claim more than they verify.
- Using Partial for a request where some fields must always be present.

## 9. Interview questions

**Q1. What is narrowing?** Refining a broad type to a more specific type using checks.

**Q2. unknown vs any?** unknown requires validation/narrowing; any opts out of type checking.

**Q3. Pick vs Omit?** Pick selects keys; Omit excludes keys.

**Q4. Does as User validate JSON?** No. It only tells the compiler to treat the value as User.

**Q5. What is a type predicate?** A function return annotation such as value is User that tells TypeScript a successful check narrows the argument.

## 10. Practice

Take an unknown API response, validate its shape, then transform it into a UserSummary type using Pick.
