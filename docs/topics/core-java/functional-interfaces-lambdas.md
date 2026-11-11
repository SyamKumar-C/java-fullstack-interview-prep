# Functional Interfaces and Lambda Expressions

## 1. Functional interface

A functional interface has exactly one abstract method (SAM). It can still have default, static and Object methods. The `@FunctionalInterface` annotation asks the compiler to verify the rule.

```java
@FunctionalInterface
interface PriceRule {
    BigDecimal apply(BigDecimal price);
}
```

## 2. Lambda expression

A lambda provides an implementation of a functional interface.

```java
PriceRule discount = price -> price.multiply(new BigDecimal("0.90"));
```

The compiler infers the target type from the context. A lambda is not a standalone method declaration; it is used where a functional interface is expected.

## 3. Common built-in functional interfaces

| Interface | Method shape | Use |
|---|---|---|
| Predicate<T> | T -> boolean | Filter/test |
| Function<T,R> | T -> R | Transform |
| Consumer<T> | T -> void | Consume |
| Supplier<T> | () -> T | Provide value |
| UnaryOperator<T> | T -> T | Same-type transform |
| BinaryOperator<T> | (T,T) -> T | Combine two same-type values |
| BiFunction<T,U,R> | (T,U) -> R | Combine two inputs |

## 4. Method references

Method references are shorter forms of lambdas when an existing method matches the required shape.

```java
names.forEach(System.out::println);
List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList();
```

Forms include static method, instance method of a particular object, instance method of an arbitrary object of a type, and constructor reference.

## 5. Variable capture

A lambda can capture local variables only when they are final or effectively final. The lambda may access an object reference, but mutating the referenced object has separate thread-safety implications.

## 6. Composition

Predicates support and/or/negate; Functions support andThen/compose; Consumers support andThen.

```java
Predicate<Employee> active = Employee::isActive;
Predicate<Employee> highPaid = e -> e.getSalary() > 100_000;
Predicate<Employee> eligible = active.and(highPaid);
```

## 7. Practical scenarios

- Predicate: filter active employees.
- Function: map Employee to EmployeeDto.
- Consumer: log each result.
- Supplier: lazily create a fallback.
- Comparator: define custom sorting.
- Method reference: simplify a direct method call.

## 8. Common mistakes

- Thinking a functional interface can have only one method total; it can have default/static methods.
- Using a lambda when a named method improves readability.
- Mutating captured local variables.
- Using side effects inside parallel stream lambdas without coordination.
- Forgetting that a lambda's target type comes from context.

## 9. Interview questions

**Q1. What is a functional interface?** An interface with one abstract method, suitable as a lambda target.

**Q2. Can it have default methods?** Yes. The single-abstract-method rule counts abstract methods.

**Q3. Predicate vs Function?** Predicate returns boolean; Function transforms T into R.

**Q4. What is effectively final?** A local variable whose value is not changed after initialization.

**Q5. Lambda vs anonymous class?** Lambdas target functional interfaces and have different this/scoping semantics; anonymous classes create a distinct class instance with their own this.

## 10. Practice

Create a list of employees and use Predicate, Function and Comparator to filter active employees, map them to names and sort them by salary.
