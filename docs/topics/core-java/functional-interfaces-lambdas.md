# Functional Interfaces and Lambdas

A functional interface has exactly one abstract method (SAM); it may also have default, static and Object-compatible methods.

```java
@FunctionalInterface
interface Transformer<T,R> { R apply(T value); }
Transformer<String,Integer> length = s -> s.length();
System.out.println(length.apply("Java")); // 4
```

## Common java.util.function types
| Type | Method | Use |
|---|---|---|
| Predicate<T> | test | Boolean condition |
| Function<T,R> | apply | Transform |
| Consumer<T> | accept | Consume |
| Supplier<T> | get | Supply |
| UnaryOperator<T> | apply | T to T |
| BinaryOperator<T> | apply | Combine two T values |

Primitive specializations such as IntPredicate reduce boxing. Method references include `String::trim`, `System.out::println` and `ArrayList::new`.

Captured local variables must be final or effectively final. A lambda captures the enclosing `this`; an anonymous inner class has its own `this`.

**Interview:** What does effectively final mean? A local variable is never reassigned after initialization.
