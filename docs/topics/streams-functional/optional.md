# Java Optional: Null-Safe Return Values

## 1. What is Optional?

`Optional<T>` represents a value that may or may not be present. It makes absence explicit in an API and can reduce accidental NullPointerExceptions.

```java
Optional<User> user = userRepository.findById(id);
```

Optional is mainly intended as a method return type. It is not a replacement for every nullable field or parameter. In Spring Data JPA, repository methods such as `findById` commonly return Optional when no row may exist.

## 2. Creating Optional

| Method | Behavior |
|---|---|
| `Optional.of(value)` | Requires non-null; throws NPE for null |
| `Optional.ofNullable(value)` | Converts null to empty |
| `Optional.empty()` | Creates an empty Optional |

```java
Optional<String> a = Optional.of("Java");
Optional<String> b = Optional.ofNullable(possiblyNull);
Optional<String> c = Optional.empty();
```

## 3. Reading and handling a value

- `isPresent()` / `isEmpty()`: check whether a value exists.
- `ifPresent(consumer)`: run an action only when present.
- `ifPresentOrElse(valueAction, emptyAction)`: handle both cases (Java 9+).
- `orElse(value)`: return a fallback value.
- `orElseGet(supplier)`: compute a fallback only when empty.
- `orElseThrow()`: throw NoSuchElementException if empty.
- `orElseThrow(supplier)`: throw a chosen exception if empty.

```java
String name = optionalName.orElse("Guest");

optionalName.ifPresent(value ->
    System.out.println("Welcome " + value));

User user = userRepository.findById(id)
    .orElseThrow(() -> new UserNotFoundException(id));
```

Avoid calling `get()` without first establishing presence. Prefer mapping, fallback, or exception methods that show the intended behavior.

## 4. orElse vs orElseGet: common interview trap

`orElse` evaluates its argument before the method call, even when the Optional has a value. `orElseGet` calls its Supplier only when the Optional is empty.

```java
User first = optionalUser.orElse(createDefaultUser());       // eager
User second = optionalUser.orElseGet(() -> createDefaultUser()); // lazy
```

If creating the fallback is expensive or has side effects, prefer `orElseGet`.

## 5. map vs flatMap

`map` transforms the value when present. If the mapping function returns null, the result becomes empty.

```java
Optional<String> city = optionalUser
    .map(User::getAddress)
    .map(Address::getCity);
```

Use `flatMap` when the mapping function already returns an Optional. It avoids nested `Optional<Optional<T>>`.

```java
Optional<Address> address = optionalUser.flatMap(User::getAddressOptional);
```

Remember:
- map: T -> R, result becomes Optional<R>.
- flatMap: T -> Optional<R>, result remains Optional<R>.

## 6. filter

`filter` keeps the value only if it matches a predicate.

```java
Optional<User> activeUser = optionalUser
    .filter(User::isActive);
```

If the Optional is empty or the predicate is false, the result is empty.

## 7. Optional with streams

From Java 9, `Optional.stream()` converts a present value to a one-element stream and an empty Optional to an empty stream.

```java
List<String> managerEmails = employees.stream()
    .map(Employee::getManagerEmailOptional)
    .flatMap(Optional::stream)
    .distinct()
    .toList();
```

This is useful when a mapping may not produce a value and you want to continue processing only present values.

## 8. Optional and API design

Good use:
```java
public Optional<Employee> findEmployee(long id) {
    return repository.findById(id);
}
```

Usually avoid:
- Optional as a JPA entity field.
- Optional as a DTO field without a clear serialization contract.
- Optional as a method parameter (overloads or nullable parameters are often clearer).
- Returning null instead of Optional.empty().
- Wrapping a collection in Optional just because the collection may be empty; return an empty collection when that is the normal result.

Optional does not make the underlying object immutable and does not guarantee thread safety for a mutable object it contains.

## 9. Common mistakes

1. Using `Optional.of(nullableValue)`.
2. Calling `get()` without checking.
3. Using `orElse(expensiveCall())` when lazy fallback is required.
4. Using map when the mapping already returns Optional (nested Optional).
5. Using Optional for every field and parameter.
6. Returning null from a method declared to return Optional.
7. Confusing an absent value with an empty collection; they often mean different things.

## 10. Interview questions

**Q1. Why use Optional?** To make a possibly absent return value explicit and encourage deliberate handling.

**Q2. of vs ofNullable?** of rejects null; ofNullable returns empty for null.

**Q3. orElse vs orElseGet?** orElse evaluates the fallback eagerly; orElseGet calls a Supplier only when empty.

**Q4. map vs flatMap?** map transforms T to R; flatMap transforms T to Optional<R> without nesting.

**Q5. Should Optional be used as an entity field?** Usually not. It is mainly designed for return values, and persistence/serialization frameworks may expect ordinary fields.

**Q6. What happens when Optional.map returns null?** The result is empty.

**Q7. How can Optional be used with streams?** Optional.stream() emits zero or one item, and flatMap(Optional::stream) removes absent values.

## 11. Practice

1. Find a user's city from Optional<User> without nested null checks.
2. Write a repository service method that throws UserNotFoundException when no user exists.
3. Demonstrate orElse versus orElseGet using a method that prints when called.
4. Convert a Stream<Optional<String>> into a Stream<String>.
5. Explain when an empty List is better than Optional<List<T>>.
