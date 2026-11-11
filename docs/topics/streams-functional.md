# Streams and Functional Programming

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 2–3 hours

A Stream is a pipeline for processing data from a source. It does not store the data itself. Stream operations describe what should happen; terminal operations trigger execution.

## 1. Functional interfaces and lambdas

A functional interface has exactly one abstract method. It can be implemented with a lambda.

```java
@FunctionalInterface
interface PriceRule {
    double apply(double price);
}

PriceRule discount = price -> price * 0.90;
System.out.println(discount.apply(100)); // 90.0
```

Common built-in interfaces:

- Predicate<T>: accepts T and returns boolean.
- Function<T,R>: converts T to R.
- Consumer<T>: accepts T and returns no result.
- Supplier<T>: provides a value.
- UnaryOperator<T>: accepts and returns the same type.

## 2. Stream pipeline

A typical pipeline has a source, zero or more intermediate operations, and one terminal operation.

```java
List<String> names = List.of("Asha", "Arun", "Bala", "Anu");

List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .sorted()
    .toList();

System.out.println(result); // [ANU, ARUN, ASHA]
```

Intermediate operations such as filter and map are lazy. A terminal operation such as toList, collect, reduce, count, or forEach starts processing.

## 3. map vs flatMap

- map transforms each input into one output.
- flatMap transforms each input into a stream and flattens the resulting streams.

```java
List<List<Integer>> groups = List.of(List.of(1, 2), List.of(3, 4));
List<Integer> all = groups.stream()
    .flatMap(List::stream)
    .toList();
```

## 4. reduce vs collect

Use reduce for combining values into one result, such as a sum or maximum. Use collect for mutable reduction into a container or structured result.

```java
int sum = List.of(1, 2, 3, 4).stream()
    .reduce(0, Integer::sum);

Map<String, Long> counts = List.of("a", "b", "a").stream()
    .collect(Collectors.groupingBy(
        Function.identity(), Collectors.counting()));
```

For numeric totals, mapToInt(...).sum() is often clearer and avoids boxing.

## 5. Grouping and partitioning

- groupingBy creates groups using a classifier.
- partitioningBy splits values into true and false groups using a predicate.

```java
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));

Map<Boolean, List<Integer>> evenOdd = List.of(1, 2, 3, 4).stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

## 6. findFirst, findAny, and matching

- findFirst returns the first element in encounter order when one exists.
- findAny may return any matching element and can be useful with parallel streams.
- anyMatch, allMatch, and noneMatch return boolean and can short-circuit.

## 7. Optional

Optional represents a value that may be absent. Use it as a return type where absence is expected; avoid blindly calling get().

```java
String displayName = users.stream()
    .filter(user -> user.id() == 10)
    .findFirst()
    .map(User::name)
    .orElse("Unknown");
```

## 8. Parallel streams

Parallel streams split work across threads using the common ForkJoinPool by default. They are not automatically faster. Consider data size, operation cost, ordering, shared state, blocking I/O, and thread-pool contention.

## 9. Interview questions

??? question "What is the difference between intermediate and terminal operations?"
    Intermediate operations return another stream and are generally lazy. Terminal operations produce a result or side effect and trigger pipeline execution.

??? question "What is the difference between map() and flatMap()?"
    map transforms each element into one result. flatMap transforms each element into a stream and flattens those streams into one stream.

??? question "When would you use reduce() instead of collect()?"
    Use reduce when the goal is to combine elements into a single value using an associative operation. Use collect when accumulating into a container such as a List or Map.

??? question "Are streams reusable?"
    No. A stream should be consumed only once. Create a new stream from the source for another pipeline.

??? question "Why can parallelStream() be slower?"
    Splitting, coordination, combining results, shared-pool contention, and small workloads can cost more than the parallel work saves.

## 10. Practice

1. Find duplicate values using groupingBy and counting.
2. Find the second-highest distinct number.
3. Group employees by department and calculate average salary.
4. Flatten a list of lists.
5. Explain why side effects inside stream operations are risky.
