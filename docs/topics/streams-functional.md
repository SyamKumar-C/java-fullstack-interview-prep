# Streams and Functional Programming

This section covers Java 8 functional programming and the Stream API in detail. For interviews, do not stop at memorizing method names: explain laziness, execution order, collector behavior, Optional semantics, and parallel trade-offs.

## Learning order

1. [Functional interfaces and lambda expressions](core-java/functional-interfaces-lambdas.md)
2. [Stream sources, operations and pipeline execution](streams-functional/stream-lifecycle.md)
3. [Collectors, grouping, partitioning and reductions](streams-functional/collectors-grouping-reducing.md)
4. [Optional and null-safe return values](streams-functional/optional.md)
5. [Parallel streams and trade-offs](streams-functional/parallel-streams.md)

## Stream operations to know

- Creation: stream, parallelStream, Stream.of, Arrays.stream, generate, iterate, IntStream.range/rangeClosed.
- Intermediate: filter, map, flatMap, distinct, sorted, peek, limit, skip, takeWhile, dropWhile.
- Terminal: toList, collect, forEach, count, reduce, min, max, findFirst, findAny.
- Matching: anyMatch, allMatch, noneMatch.
- Primitive streams: mapToInt, mapToLong, mapToDouble, sum, average, summaryStatistics, boxed.

## Collector patterns to practise

- toList, toSet, toCollection and joining.
- groupingBy with counting, summingInt, averagingInt, mapping and summarizingInt.
- partitioningBy with a predicate.
- toMap with a duplicate-key merge function.
- collectingAndThen and reducing.

## Coding problems

- Find duplicate elements and their counts.
- Find unique elements and frequency maps.
- Find highest and second-highest distinct values.
- Group employees by department and count each group.
- Calculate average salary by department.
- Partition numbers into even and odd.
- Flatten nested lists.
- Convert a list of orders into customerId -> total amount.
- Find the first matching element and explain short-circuiting.

## Interview checklist

You should be able to explain:
- Functional interface vs ordinary interface; Predicate, Function, Consumer and Supplier.
- Lambda target typing, method references, effectively final capture and lambda `this`.
- Stream source/intermediate/terminal lifecycle and lazy evaluation.
- map vs flatMap; reduce vs collect; findFirst vs findAny.
- Encounter order, short-circuiting, stream reuse and side effects.
- groupingBy vs partitioningBy; downstream collectors; duplicate-key handling in toMap.
- Optional.of vs ofNullable; map vs flatMap; orElse vs orElseGet.
- Parallel stream splitting, common pool, ordering, associative reduction and performance trade-offs.

## Important version notes

- Streams and the main functional interfaces arrived in Java 8.
- Optional.ifPresentOrElse, Optional.or, Optional.stream, and Stream.takeWhile/dropWhile were added in Java 9.
- Stream.toList() was added in Java 16 and returns an unmodifiable List.
- Use the Java version configured by the project when selecting APIs.
