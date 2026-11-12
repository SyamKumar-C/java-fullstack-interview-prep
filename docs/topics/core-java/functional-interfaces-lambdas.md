# Functional Interfaces and Lambda Expressions

## 1. Functional interface: the SAM rule

A functional interface has exactly one **abstract method** (Single Abstract Method / SAM). It may still contain multiple default and static methods. Methods matching public methods of `Object` do not create extra SAM requirements.

```java
@FunctionalInterface
interface DiscountRule {
    BigDecimal apply(BigDecimal price);

    default String description() {
        return "Discount rule";
    }
}
```

`@FunctionalInterface` is optional, but useful: the compiler reports an error if the interface stops being functional.

A lambda is not a separate type by itself. It is an implementation of a functional interface, and the compiler gets its target type from the assignment, method parameter, or cast.

```java
DiscountRule tenPercent = price ->
    price.multiply(new BigDecimal("0.90"));

DiscountRule noDiscount = price -> price;
```

## 2. Built-in functional interfaces

All are in `java.util.function`.

| Interface | Abstract method shape | Meaning | Example |
|---|---|---|---|
| Predicate<T> | T -> boolean | Test a condition | `e -> e.isActive()` |
| Function<T,R> | T -> R | Transform a value | `Employee::getName` |
| Consumer<T> | T -> void | Use a value | `System.out::println` |
| Supplier<T> | () -> T | Provide a value | `UUID::randomUUID` |
| UnaryOperator<T> | T -> T | Transform same type | `s -> s.trim()` |
| BinaryOperator<T> | (T,T) -> T | Combine same type | `Integer::sum` |
| BiFunction<T,U,R> | (T,U) -> R | Two inputs to result | `(a,b) -> a+b` |
| BiPredicate<T,U> | (T,U) -> boolean | Test two inputs | `String::startsWith` |
| BiConsumer<T,U> | (T,U) -> void | Consume two inputs | `map::put` |

Primitive specializations such as `IntPredicate`, `IntFunction<R>`, `ToIntFunction<T>`, and `IntBinaryOperator` can avoid boxing and unboxing.

## 3. Lambda syntax and type inference

```java
// No parameters
Runnable task = () -> System.out.println("Running");

// One parameter: parentheses are optional
Predicate<String> nonEmpty = value -> !value.isEmpty();

// Multiple parameters
Comparator<String> byLength =
    (left, right) -> Integer.compare(left.length(), right.length());

// Block body must return a value when the SAM method has a return type
Function<String, Integer> length = value -> {
    String normalized = value.trim();
    return normalized.length();
};
```

The compiler infers parameter types from the target functional interface. Add explicit types only when they improve clarity or resolve ambiguity.

## 4. Method references

A method reference is shorthand for a lambda that calls an existing method.

| Form | Example | Similar lambda |
|---|---|---|
| Static method | `Integer::parseInt` | `s -> Integer.parseInt(s)` |
| Instance method of a particular object | `logger::info` | `s -> logger.info(s)` |
| Instance method of an arbitrary object of a type | `String::toLowerCase` | `s -> s.toLowerCase()` |
| Constructor | `ArrayList::new` | `() -> new ArrayList<>()` |

The method reference must match the target method's parameter and return types. It is not automatically better than a lambda; choose the clearer form.

## 5. Variable capture and `this`

A lambda can read local variables only when they are final or effectively final.

```java
int minimum = 100; // effectively final
Predicate<Employee> eligible = e -> e.getSalary() >= minimum;
```

This does not compile if `minimum` is later reassigned. A captured object reference can still point to a mutable object; that does not make mutation thread-safe.

Inside a lambda, `this` refers to the enclosing object. Inside an anonymous inner class, `this` refers to that anonymous class instance. This difference is a common interview question.

## 6. Composing functions

```java
Predicate<Employee> active = Employee::isActive;
Predicate<Employee> wellPaid = e -> e.getSalary() >= 100_000;
Predicate<Employee> eligible = active.and(wellPaid);

Function<String, String> trim = String::trim;
Function<String, String> upper = String::toUpperCase;
Function<String, String> normalize = trim.andThen(upper);

Function<Integer, Integer> addOne = n -> n + 1;
Function<Integer, Integer> doubleIt = n -> n * 2;
Function<Integer, Integer> doubleThenAdd = addOne.compose(doubleIt);
// doubleIt runs first, then addOne
```

- Predicate: `and`, `or`, `negate`.
- Function: `andThen` runs this function first; `compose` runs the supplied function first.
- Consumer: `andThen` runs consumers in sequence.

Predicate composition uses short-circuit evaluation: `A.and(B)` does not evaluate B when A is false.

## 7. Where these appear in real Java code

- Streams: `filter(Predicate)`, `map(Function)`, `forEach(Consumer)`.
- Sorting: `Comparator<T>`.
- Lazy fallback: `Optional.orElseGet(Supplier)`.
- Async work: `CompletableFuture.supplyAsync(Supplier)`.
- Spring: callbacks, event listeners, and custom strategies can use functional interfaces.

Prefer a named method or class when the logic is long, reused, or needs a meaningful name. Lambdas should make code easier to read, not compress complex business logic into one line.

## 8. Common mistakes

1. Saying a functional interface can contain only one method total. The rule is one abstract method.
2. Trying to reassign a captured local variable.
3. Assuming a lambda creates a new scope for `this`.
4. Using raw or overly generic functional interfaces when a primitive specialization is clearer.
5. Writing side effects into stream predicates or mapping functions.
6. Assuming a method reference can be used even when its signature does not match the target SAM.

## 9. Interview questions

**Q1. What is a functional interface?** An interface with one abstract method; it is the target type for a lambda or method reference.

**Q2. Can it have default and static methods?** Yes. They are not additional abstract methods.

**Q3. Predicate vs Function?** Predicate tests and returns boolean; Function transforms T into R.

**Q4. Consumer vs Supplier?** Consumer accepts a value and returns nothing; Supplier accepts no input and provides a value.

**Q5. What does effectively final mean?** A local variable is assigned once and is not reassigned after initialization.

**Q6. Lambda vs anonymous class?** A lambda implements a functional interface and uses the enclosing `this`; an anonymous class creates its own class instance and its own `this`.

**Q7. What is the difference between `compose` and `andThen`?** For Function, `compose` applies the supplied function first; `andThen` applies this function first.

## 10. Practice

1. Define a functional interface for calculating tax and implement two tax rules with lambdas.
2. Filter active employees whose salary exceeds a threshold using Predicate composition.
3. Convert a list of Employee objects into employee-name strings using Function.
4. Sort employees by department and then salary using Comparator chaining.
5. Explain why changing a local variable captured by a lambda is not allowed.
