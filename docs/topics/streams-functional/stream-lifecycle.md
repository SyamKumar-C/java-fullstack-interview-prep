# Java Stream API: Lifecycle and Operations

## 1. What is a Stream?

A Stream is a pipeline for processing data from a source. It is not a data structure and does not store elements itself. It describes operations over a source such as a Collection, array, generator, or I/O source.

A typical pipeline has:
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

## 2. Stream creation

```java
Stream<String> fromCollection = names.stream();
Stream<Integer> fromArray = Arrays.stream(new int[]{1, 2, 3}).boxed();
Stream<Integer> generated = Stream.iterate(1, n -> n + 1).limit(5);
Stream<String> direct = Stream.of("Java", "Spring");
```

Use primitive streams `IntStream`, `LongStream`, and `DoubleStream` for numeric work to avoid unnecessary boxing.

## 3. Intermediate operations

Intermediate operations return another Stream and are generally lazy.

- filter: keep matching elements.
- map: transform each element.
- flatMap: transform each element into a stream and flatten the results.
- distinct: remove duplicates according to equals.
- sorted: sort elements.
- peek: observe elements mainly for debugging; do not use it for business side effects.
- limit/skip: restrict the number of elements.

```java
List<String> words = sentences.stream()
    .flatMap(sentence -> Arrays.stream(sentence.split(" ")))
    .map(String::toLowerCase)
    .distinct()
    .toList();
```

## 4. Terminal operations

A terminal operation triggers pipeline evaluation and consumes the stream.

- collect / toList: build a result.
- forEach: perform an action for each element.
- count: number of elements.
- reduce: combine elements into one result.
- min / max: find an extreme.
- findFirst / findAny: find an element.
- anyMatch / allMatch / noneMatch: test predicates.

A stream should be used only once. After a terminal operation, it is consumed and cannot be reused.

## 5. Laziness and short-circuiting

Intermediate operations do not normally run until a terminal operation is called.

```java
Stream<Integer> s = Stream.of(1, 2, 3)
    .filter(n -> {
        System.out.println("checking " + n);
        return n > 1;
    });
// No output yet
long count = s.count(); // pipeline executes here
```

Short-circuiting operations such as findFirst, anyMatch, and limit may stop processing once enough information is available.

## 6. map versus flatMap

`map` produces one output for each input. `flatMap` produces a stream for each input and flattens all those streams into one.

```java
List<List<Integer>> nested = List.of(List.of(1, 2), List.of(3, 4));
List<Integer> flat = nested.stream().flatMap(List::stream).toList();
// [1, 2, 3, 4]
```

## 7. reduce versus collect

Use reduce for immutable aggregation to one value, such as sum or maximum. Use collect for mutable accumulation into containers such as lists, maps, or grouped summaries.

Do not use reduce with a mutable identity such as a shared ArrayList. Use collect instead.

## 8. Encounter order

Some sources have a defined encounter order, such as a List. Others may not, such as HashSet. Operations such as sorted establish an order; unordered can relax order constraints. Parallel execution may change the order in which actions run.

## 9. Side effects and stream reuse

Avoid mutating shared external state from map/filter. Prefer transformations and collectors. Do not reuse a Stream instance after a terminal operation; create a new stream from the source.

## 10. Interview questions and answers

**Q1. What are intermediate and terminal operations?**  
Intermediate operations build a pipeline and return a Stream; terminal operations trigger evaluation and produce a result or side effect.

**Q2. Are streams lazy?**  
Intermediate operations are lazy. Evaluation begins when a terminal operation is invoked.

**Q3. Can we reuse a stream?**  
No. A stream is consumed after a terminal operation. Create another stream from the source.

**Q4. Difference between map and flatMap?**  
map transforms one input to one output. flatMap maps each input to a stream and flattens those streams.

**Q5. Why avoid side effects?**  
They make execution order and parallel behavior harder to reason about, and can introduce race conditions.

**Q6. What is short-circuiting?**  
An operation can finish without processing every element, for example anyMatch after the first match.

## 11. Practice exercises

1. Filter even numbers, square them, and collect to a list.
2. Flatten a list of lists.
3. Find the first employee whose salary is above a threshold.
4. Check whether all orders have positive totals.
5. Explain why a filter's print statement does not run until a terminal operation is called.

## 12. Quick revision

Source -> intermediate operations -> terminal operation. Intermediate operations are lazy; terminal operations consume the stream. map transforms; flatMap flattens.
