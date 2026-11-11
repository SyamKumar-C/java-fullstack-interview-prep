# Parallel Streams

Parallel streams split work into tasks, commonly using the ForkJoin common pool, process tasks concurrently and combine results.

```java
long count = values.parallelStream().filter(this::expensiveCheck).count();
```

Potentially useful for large, CPU-bound, independent workloads where splitting/combining costs are small. Often harmful for small collections, blocking I/O, shared mutable state, strict ordering or common-pool contention.

Operations should be stateless and non-interfering. Reduction functions must be associative. Do not assume forEach order; use forEachOrdered when encounter order is required.

**Interview:** Are parallel streams always faster? No; scheduling, splitting, merging and contention can make them slower.
