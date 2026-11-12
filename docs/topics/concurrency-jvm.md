# Concurrency and JVM

Concurrency is important for backend interviews because Spring Boot services handle many requests at the same time. Understand how threads share data, how Java guarantees visibility, how to control task execution, and how the JVM manages memory.

## Learning order
1. [synchronized, volatile, Locks and coordination](concurrency-jvm/synchronization-locks.md)
2. [Atomic classes, deadlocks and thread safety](concurrency-jvm/atomic-deadlocks-thread-safety.md)
3. [ExecutorService, Future and CompletableFuture](concurrency-jvm/executors-futures.md)
4. [Virtual threads (Java 21)](concurrency-jvm/virtual-threads.md)
5. [JVM memory areas, GC and memory leaks](concurrency-jvm/jvm-memory-gc.md)

## Core questions to master
- What is the difference between a process and a thread?
- Why is count++ not atomic?
- What is the difference between visibility and atomicity?
- What does happens-before mean?
- synchronized vs volatile vs AtomicInteger?
- How do deadlock, starvation and livelock differ?
- Runnable vs Callable; execute vs submit.
- Future vs CompletableFuture; thenApply vs thenCompose.
- Why can an unbounded executor queue be dangerous?
- When should you use a bounded thread pool, virtual threads or reactive I/O?
- Why do virtual threads not remove database connection limits?
- What are heap, stack, metaspace and GC roots?
- How do you investigate a production memory leak?

## Important design rule
Choose concurrency tools based on the resource being protected:
- CPU work needs bounded parallelism.
- Blocking I/O needs controlled concurrency and timeouts.
- Shared in-memory state needs thread-safe access.
- Shared data across application instances needs database or distributed coordination.
- Large numbers of waiting tasks may benefit from virtual threads, but downstream systems still need limits.

Use the [Core Java Interview Coverage Checklist](core-java/interview-coverage-checklist.md) to track detailed coverage.
