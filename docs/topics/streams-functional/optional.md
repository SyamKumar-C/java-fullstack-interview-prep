# Optional

Optional represents a value that may be absent, making absence explicit in return types.

```java
Optional<User> user = repository.findById(id);
String name = user.map(User::getName).orElse("Unknown");
```

Methods: of (rejects null), ofNullable, empty, map, flatMap, filter, orElse, orElseGet and orElseThrow.

Important distinction: orElse evaluates its fallback eagerly; orElseGet calls the supplier only when empty. Avoid get() without checking presence. Optional is mainly intended as a return type, not a replacement for every nullable field.

**Interview:** orElse vs orElseGet? Eager fallback expression vs lazy supplier.
