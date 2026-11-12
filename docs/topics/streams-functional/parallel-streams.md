# Java Parallel Streams: Execution and Trade-offs

## 1. Sequential vs parallel stream

A sequential stream processes its pipeline sequentially. A parallel stream can split the source into partitions and process those partitions concurrently.

```java
long evenCount = numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .count();
```

Parallel execution does not guarantee a faster result. It adds splitting, scheduling, coordination and combining overhead.

## 2. How parallel streams work

1. A source is represented by a Spliterator.
2. The source is split into smaller pieces when possible.
3. Tasks process different pieces.
4. Partial results are combined.
5. The final result is returned.

Parallel streams commonly use the `ForkJoinPool.commonPool()`. A parallel stream does not normally create a dedicated thread pool for every call.

## 3. When parallel streams may help

- The data set is large enough to offset overhead.
- Work per element is CPU-intensive.
- Each element can be processed independently.
- The source splits efficiently (arrays and ArrayList are generally good examples).
- The reduction is associative and combines partial results correctly.
- Measurements show a real improvement under realistic load.

## 4. When to avoid them

- Small collections.
- Blocking database, file, or network calls.
- Shared mutable state.
- Operations that depend on strict encounter order.
- Sources that split poorly.
- When common-pool work competes with other application tasks.
- When predictable concurrency limits are required.

For remote API calls, use a client and executor/concurrency limit designed for that work rather than assuming parallel streams provide backpressure or a safe request limit.

## 5. Stateless and non-interfering functions

Stream functions should not modify the source while it is being processed and should avoid shared mutable state.

Unsafe:
```java
List<Integer> output = new ArrayList<>();
numbers.parallelStream().forEach(output::add); // data race
```

Better:
```java
List<Integer> output = numbers.parallelStream()
    .filter(n -> n > 10)
    .toList();
```

Use built-in collectors and reductions instead of mutating shared containers.

## 6. Ordering

- `forEach` does not guarantee encounter order in parallel execution.
- `forEachOrdered` preserves encounter order but may reduce parallelism.
- `findFirst` respects encounter order when one exists.
- `findAny` may return any matching element.
- `unordered()` can relax order requirements when order is irrelevant.

## 7. Reduction requirements

Parallel reduction can combine partial results in different groupings. The operation must be associative, and the identity must be correct.

```java
int sum = numbers.parallelStream()
    .reduce(0, Integer::sum);
```

A subtraction reduction is not associative and can produce surprising results when parallelized. Avoid side effects inside reduce/collect functions.

## 8. Performance measurement

Do not compare one run of sequential code with one run of parallel code and draw a conclusion. Warm up the JVM, use representative data, repeat measurements, avoid including unrelated work, and use a benchmark tool such as JMH for serious performance comparisons. Also measure under realistic concurrent application load.

## 9. Practical scenario

A batch job calculates an independent CPU-heavy score for a large in-memory collection. A parallel stream may help after measurement. Calling a remote service for every item is usually a poor fit because blocking and uncontrolled concurrency can overload the remote service or common pool.

## 10. Common mistakes

1. Assuming parallel is always faster.
2. Mutating an ArrayList from parallel forEach.
3. Performing blocking network calls in the common pool.
4. Ignoring encounter-order requirements.
5. Using non-associative reduction.
6. Assuming a parallel stream provides a dedicated executor or request backpressure.
7. Benchmarking without warm-up or representative load.

## 11. Interview questions

**Q1. How do parallel streams work?** They split a source into tasks, process partitions concurrently, and combine partial results.

**Q2. Which pool is commonly used?** ForkJoinPool.commonPool().

**Q3. Why can parallel streams be slower?** Splitting, scheduling and combining overhead may exceed the work saved.

**Q4. Why is shared mutable state dangerous?** Multiple workers can write at the same time, causing race conditions and incorrect results.

**Q5. Is forEach order guaranteed?** No. Use forEachOrdered if encounter order is required.

**Q6. Are parallel streams good for database or HTTP calls?** Usually not. Blocking I/O and uncontrolled concurrency make them a poor default.

## 12. Practice

Compare sequential and parallel processing for a CPU-heavy calculation at several input sizes. Verify that results match, measure repeatedly, and explain when the parallel version stops being beneficial.
