# Java Stream API: Sources, Operations and Pipeline Execution

## 1. What is a Stream?

A Stream is a pipeline for processing data. It is not a collection and does not store elements. A stream describes *what processing should happen* to elements from a source such as a Collection, array, generated sequence, or I/O source.

A normal pipeline has three parts:

1. Source
2. Zero or more intermediate operations
3. One terminal operation

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .sorted()
    .toList();
```

## 2. Creating streams

```java
Stream<String> fromCollection = names.stream();
Stream<String> parallel = names.parallelStream();
Stream<Integer> fromValues = Stream.of(10, 20, 30);
IntStream fromArray = Arrays.stream(new int[]{1, 2, 3});
Stream<Integer> generated = Stream.iterate(1, n -> n + 1).limit(5);
Stream<Double> randoms = Stream.generate(Math::random).limit(3);
```

Use `IntStream`, `LongStream`, and `DoubleStream` for primitive numeric processing to avoid unnecessary wrapper objects.

```java
int total = IntStream.rangeClosed(1, 5).sum(); // 15
List<Integer> boxed = IntStream.range(1, 4).boxed().toList();
```

Be careful with infinite streams from `iterate` and `generate`: use a short-circuiting terminal operation or a limit.

## 3. Intermediate operations

Intermediate operations return another stream and are generally lazy.

| Operation | Purpose | Example |
|---|---|---|
| filter | Keep matching elements | `filter(n -> n > 10)` |
| map | Transform each element | `map(String::length)` |
| flatMap | Transform and flatten nested streams | `flatMap(List::stream)` |
| distinct | Remove duplicates using equals | `distinct()` |
| sorted | Sort by natural/custom order | `sorted(Comparator.reverseOrder())` |
| peek | Observe elements, mainly for debugging | `peek(System.out::println)` |
| limit | Keep at most N elements | `limit(5)` |
| skip | Ignore first N elements | `skip(10)` |
| takeWhile | Keep while predicate remains true | `takeWhile(n -> n < 10)` |
| dropWhile | Drop while predicate remains true | `dropWhile(n -> n < 10)` |

`takeWhile` and `dropWhile` are available from Java 9. `peek` is not a reliable place for business side effects: optimizations and short-circuiting can mean not every expected action runs.

## 4. map vs flatMap

`map` maps one input to one output. `flatMap` maps each input to a stream and flattens the nested streams into one stream.

```java
List<List<Integer>> nested =
    List.of(List.of(1, 2), List.of(3, 4));

List<Integer> flattened = nested.stream()
    .flatMap(List::stream)
    .toList(); // [1, 2, 3, 4]
```

Example: each order has multiple line items. Use `flatMap(order -> order.getItems().stream())` to process all line items as one stream.

## 5. Terminal operations

Terminal operations trigger evaluation and consume the stream.

| Operation | Result |
|---|---|
| `toList()` | Unmodifiable List result (Java 16+) |
| `collect(...)` | Result described by a Collector |
| `forEach(...)` | Performs an action |
| `count()` | Number of elements |
| `reduce(...)` | Combines elements into one value |
| `min(...)` / `max(...)` | Optional minimum/maximum |
| `findFirst()` / `findAny()` | Optional matching element |
| `anyMatch` / `allMatch` / `noneMatch` | Boolean predicate result |

The List returned by `Stream.toList()` is unmodifiable. If you need a mutable ArrayList, use `collect(Collectors.toCollection(ArrayList::new))`.

A stream instance is single-use. After a terminal operation, do not try to run another terminal operation on that same stream.

## 6. Lazy evaluation and operation order

Intermediate operations normally do no work until a terminal operation is invoked.

```java
Stream<Integer> stream = Stream.of(1, 2, 3)
    .filter(n -> {
        System.out.println("filter " + n);
        return n > 1;
    });
// No output has happened yet.
long count = stream.count(); // evaluation starts here
```

Operations can be interleaved per element. For example, `filter().map().findFirst()` can stop after finding the first match; it does not necessarily map the entire source first.

Compare:
```java
numbers.stream()
    .map(this::expensiveCalculation)
    .filter(this::isEligible)
    .findFirst();
```
with:
```java
numbers.stream()
    .filter(this::isEligible)
    .map(this::expensiveCalculation)
    .findFirst();
```
If the filter is cheap and selective, filtering first avoids expensive work for rejected elements. The order must still preserve the required business result.

## 7. Short-circuiting operations

Short-circuiting operations can finish without processing every element.

- `anyMatch`: stops once one element matches.
- `allMatch`: stops once one element fails.
- `noneMatch`: stops once one element matches.
- `findFirst` / `findAny`: stop after a result is found.
- `limit`: bounds the number of elements requested from upstream.

Empty stream behavior: `anyMatch` is false, `allMatch` is true, and `noneMatch` is true. This is intentional logical behavior (vacuous truth).

## 8. reduce vs collect

Use `reduce` when combining values into one immutable result, such as sum or maximum. Use `collect` when accumulating into a mutable result container such as a List, Map, or grouped summary.

```java
int sum = numbers.stream().reduce(0, Integer::sum);

List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toCollection(ArrayList::new));
```

Do not use a shared mutable object such as an ArrayList as the identity for `reduce`. Use a Collector. For parallel reductions, the operation must be associative and the identity/combiner must obey the reduction contract.

## 9. Encounter order and side effects

Lists have encounter order; HashSet does not promise one. `sorted` establishes order. `forEach` on a parallel stream may run actions in a different order; `forEachOrdered` preserves encounter order but can reduce parallel performance.

Avoid modifying the source while it is being processed (interference) and avoid shared mutable state in stream functions. Prefer mapping and collecting to external mutation.

## 10. Common interview coding patterns

```java
// Even numbers
List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0).toList();

// Names, uppercase
List<String> namesUpper = employees.stream()
    .map(Employee::getName)
    .map(String::toUpperCase).toList();

// Total using primitive stream
int totalSalary = employees.stream()
    .mapToInt(Employee::getSalary).sum();

// Highest salary
Optional<Employee> highestPaid = employees.stream()
    .max(Comparator.comparingInt(Employee::getSalary));

// Any inactive employee?
boolean hasInactive = employees.stream().anyMatch(e -> !e.isActive());
```

## 11. Common mistakes

- Reusing a stream after a terminal operation.
- Expecting intermediate operations to execute without a terminal operation.
- Using `map` when nested streams need flattening.
- Using `peek` for required business logic.
- Assuming HashSet has a stable order.
- Assuming `Stream.toList()` returns a mutable list.
- Using side effects in parallel operations.
- Using `reduce` for mutable containers instead of `collect`.

## 12. Interview questions

**Q1. What are intermediate and terminal operations?** Intermediate operations build a lazy pipeline and return a stream; terminal operations trigger evaluation and produce a result or side effect.

**Q2. Are streams lazy?** Intermediate operations are lazy; work starts when a terminal operation is called.

**Q3. Can a stream be reused?** No. A stream is consumed after a terminal operation.

**Q4. map vs flatMap?** map transforms each input; flatMap transforms each input into a stream and flattens those streams.

**Q5. findFirst vs findAny?** findFirst respects encounter order when one exists; findAny may return any element and can be more flexible in parallel processing.

**Q6. anyMatch vs allMatch vs noneMatch?** They test whether at least one, every, or no element matches. Their results on an empty stream are false, true, and true respectively.

**Q7. What is the difference between Stream.toList() and Collectors.toList()?** Stream.toList() returns an unmodifiable list. Collectors.toList() does not guarantee a particular implementation or mutability contract; use toCollection(ArrayList::new) when mutability is required.

## 13. Practice exercises

1. Filter even numbers, square them, and collect to a list.
2. Flatten a list of lists and remove duplicates.
3. Find the first employee with salary above a threshold.
4. Check whether all orders have positive totals.
5. Find the second-highest distinct number.
6. Explain why a filter print statement does not run until a terminal operation is invoked.
