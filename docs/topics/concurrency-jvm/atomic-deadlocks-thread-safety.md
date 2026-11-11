# Atomic Classes, Deadlocks and Thread Safety

## Atomic classes
AtomicInteger and related classes use compare-and-set (CAS) for atomic updates without an explicit monitor.
```java
AtomicInteger counter = new AtomicInteger();
counter.incrementAndGet();
```
LongAdder can perform better under high contention, but sum() is not an atomic snapshot during concurrent updates.

## Deadlock
Threads deadlock when each waits for a lock held by another. Prevent with consistent lock ordering, short critical sections, avoiding unknown calls while holding locks and timed tryLock where suitable.

## Thread safety
A class is thread-safe when it remains correct under concurrent use. Common approaches: immutability, thread confinement, synchronization and concurrent data structures.

**Interview:** What is CAS? Update only if the current value matches an expected value. What is ABA? A value changes A→B→A and a simple CAS may not detect the intermediate change.
