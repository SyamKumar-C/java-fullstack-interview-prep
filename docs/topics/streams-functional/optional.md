# Java Optional

## 1. What is Optional?

Optional<T> represents a value that may be present or absent. It makes possible absence explicit in an API and can reduce accidental null handling.

```java
Optional<User> user = userRepository.findById(id);
```

Optional is mainly intended as a return type. Avoid using it indiscriminately for fields, method parameters or collection elements.

## 2. Creating Optional

- `Optional.of(value)`: value must not be null.
- `Optional.ofNullable(value)`: accepts null and creates empty when null.
- `Optional.empty()`: no value.

## 3. Reading a value

- `isPresent()` / `isEmpty()`: check presence.
- `ifPresent(consumer)`: act when present.
- `orElse(value)`: return fallback; the fallback expression is evaluated eagerly.
- `orElseGet(supplier)`: compute fallback only when empty.
- `orElseThrow()`: throw if empty.
- `map`: transform present value.
- `flatMap`: transform to another Optional without nesting.
- `filter`: keep the value only if predicate matches.

```java
String city = userRepository.findById(id)
    .map(User::getAddress)
    .map(Address::getCity)
    .orElse("Unknown");
```

If getAddress can return null, Optional.map converts the mapped null to empty. Use flatMap when the mapping function already returns Optional.

## 4. orElse vs orElseGet

```java
User a = optional.orElse(createDefaultUser());       // eager
User b = optional.orElseGet(() -> createDefaultUser()); // lazy
```

Use orElseGet when fallback creation is expensive or has side effects.

## 5. Optional with streams

Optional can be converted to a stream in modern Java using `stream()`, which yields zero or one element. This is useful when flattening optional results.

## 6. Practical scenarios

- Repository lookup returns Optional<User>.
- Find a nested value without repeated null checks.
- Apply a validation predicate and return a fallback/error.
- Avoid returning null from a method that may have no result.

## 7. Common mistakes

- Calling get() without checking presence.
- Using Optional.of with a nullable value.
- Using Optional as a replacement for every null in an object model.
- Confusing map and flatMap.
- Using orElse when the fallback should be lazy.

## 8. Interview questions

**Q1. Why use Optional?** To represent possible absence explicitly in an API.

**Q2. of vs ofNullable?** of rejects null; ofNullable creates empty for null.

**Q3. orElse vs orElseGet?** orElse evaluates its argument eagerly; orElseGet invokes a supplier only when empty.

**Q4. map vs flatMap?** map wraps a mapped value; flatMap expects a mapping that already returns Optional and avoids nesting.

**Q5. Should Optional be used as an entity field?** Usually no; it is primarily intended for return values.

## 9. Practice

Write a method that finds an employee's manager's email using Optional.map/flatMap and returns a clear fallback when any relationship is absent.
