# Atomicity, Deadlocks and Thread Safety

## 1. Thread safety

A class is thread-safe when it behaves correctly when multiple threads access it at the same time. The key question is whether shared mutable state can be observed or changed inconsistently.

Ways to protect state include immutability, thread confinement, synchronization, locks, and atomic classes.

## 2. Race condition

A race condition occurs when the result depends on the timing/interleaving of threads.

```java
class Counter {
    private int count;
    void increment() { count++; }
}
```

`count++` is a read-modify-write sequence, not one atomic operation. Two threads can read the same value and overwrite each other's update.

Fix with synchronization:

```java
class Counter {
    private int count;
    synchronized void increment() { count++; }
    synchronized int get() { return count; }
}
```

Or use `AtomicInteger` for a simple counter.

## 3. Atomic classes and CAS

Classes such as AtomicInteger use compare-and-set (CAS). CAS updates a value only if it still equals an expected value. It can avoid blocking for simple state changes, but compound invariants may need a lock.

```java
AtomicInteger count = new AtomicInteger();
count.incrementAndGet();
```

For multiple related fields that must change together, use a lock or a single immutable state updated atomically.

## 4. Deadlock

Deadlock occurs when threads wait forever for locks held by each other.

Example: Thread 1 holds lock A and waits for B; Thread 2 holds B and waits for A.

Prevention:
- Acquire locks in a consistent global order.
- Keep critical sections short.
- Avoid calling unknown/external code while holding locks.
- Use timed `tryLock` when suitable.
- Reduce the number of nested locks.

## 5. Liveness problems

- Deadlock: no involved thread can proceed.
- Starvation: a thread repeatedly fails to get CPU/lock access.
- Livelock: threads keep reacting to each other but make no progress.

## 6. Java Memory Model and visibility

A thread may not immediately observe another thread's write without a happens-before relationship. `volatile` provides visibility and ordering guarantees for reads/writes of that variable, but does not make compound operations such as `count++` atomic.

Synchronization, volatile, thread start/join and concurrency utilities establish relevant happens-before relationships.

## 7. Practical scenario

A shared in-memory inventory counter is updated by many request threads. A plain int can lose updates. Use AtomicInteger if only one number changes independently; use a database transaction/locking strategy if stock updates must remain consistent across application instances.

## 8. Common mistakes

- Assuming volatile makes increment atomic.
- Assuming synchronized protects data if not all accesses use the same lock.
- Acquiring multiple locks in inconsistent orders.
- Using atomics for a multi-field invariant.
- Assuming an in-memory lock coordinates multiple application servers.

## 9. Interview questions

**Q1. What is a race condition?** A timing-dependent bug caused by concurrent access to shared state.

**Q2. AtomicInteger vs volatile int?** AtomicInteger supports atomic read-modify-write operations; volatile int provides visibility but not atomic increment.

**Q3. What causes deadlock?** Circular waiting for locks, with each thread holding a resource another needs.

**Q4. How do you prevent deadlock?** Consistent lock ordering, short critical sections and avoiding nested locks where possible.

**Q5. What is starvation?** A thread cannot get enough access to a resource to make progress.

## 10. Practice

Create two threads that increment a shared counter 100,000 times. Compare plain int, synchronized and AtomicInteger. Then create a two-lock deadlock and fix it by ordering lock acquisition.
