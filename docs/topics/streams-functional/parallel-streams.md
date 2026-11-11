# Java Parallel Streams

## 1. Sequential vs parallel stream

A sequential stream processes elements in one pipeline on the calling thread in encounter order where the operation requires it. A parallel stream splits work into tasks and processes them concurrently, commonly using the ForkJoin common pool.

```java
long count = numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .count();
```

Parallel does not guarantee faster execution.

## 2. How parallel processing works

The source is split using a Spliterator. Tasks process partitions, then combine partial results. Performance depends on source splitting, task size, operation cost, data size and combination overhead.

## 3. Good use cases

- Large data sets.
- CPU-intensive, independent operations.
- Efficiently splittable sources such as arrays or ArrayList.
- Associative reduction operations with correct identity/combiner behavior.

## 4. Poor use cases

- Small collections where overhead dominates.
- Blocking I/O operations.
- Shared mutable state.
- Operations with required strict ordering.
- Sources that split poorly.
- Workloads that compete with unrelated tasks in the common pool.

## 5. Stateless and non-interfering behavior

Stream functions should not modify the source while it is being processed and should avoid shared mutable state.

Bad:

```java
List<Integer> output = new ArrayList<>();
numbers.parallelStream().forEach(output::add); // unsafe
```

Better:

```java
List<Integer> output = numbers.parallelStream()
    .filter(n -> n > 10)
    .toList();
```

Use built-in collectors/reductions instead of shared mutation.

## 6. Ordering

forEach may process in arbitrary order. forEachOrdered preserves encounter order but can reduce parallel benefits. unordered() can relax ordering constraints when valid for the problem.

## 7. Common pool considerations

Parallel streams commonly use ForkJoinPool.commonPool(). Blocking tasks can reduce its usefulness for other work. Do not assume a parallel stream creates a dedicated thread pool for each call.

## 8. Practical scenario

A batch job calculates independent CPU-heavy scores for hundreds of thousands of records. Parallel streams may help after measuring. Calling a remote API for every item is usually a poor fit; use a controlled asynchronous client and explicit concurrency/backpressure.

## 9. Common mistakes

- Assuming parallel is always faster.
- Mutating a shared ArrayList from parallel forEach.
- Performing blocking network calls in the common pool.
- Ignoring order requirements.
- Using non-associative reductions that produce incorrect results.

## 10. Interview questions

**Q1. How do parallel streams work?** They split a source into tasks, process partitions concurrently and combine results.

**Q2. Which pool is commonly used?** ForkJoinPool.commonPool.

**Q3. Why can parallel streams be slower?** Splitting, scheduling and combining overhead can exceed the work saved.

**Q4. Why is shared mutable state dangerous?** Concurrent writes can race and corrupt results.

**Q5. Is forEach order guaranteed?** Not for parallel streams; use forEachOrdered when encounter order is required.

## 11. Practice

Compare sequential and parallel processing for a CPU-heavy calculation at different input sizes. Verify results are identical and record timings rather than assuming parallel wins.
