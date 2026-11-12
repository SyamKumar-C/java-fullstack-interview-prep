# Atomic Classes, Deadlocks and Thread Safety

## 1. What does thread-safe mean?

A class is thread-safe when it behaves correctly under concurrent access, according to its contract. Thread safety is not just "using synchronized"; it is about protecting shared state and invariants.

Common strategies:
- Immutability: state cannot change after construction.
- Thread confinement: only one thread accesses the state.
- Synchronization/locks: coordinate access.
- Atomic variables: atomic operations on individual values.
- Concurrent collections: use data structures designed for concurrent access.

## 2. Race condition and lost update

`count++` is not one indivisible action. It is effectively read, add one, then write.

```java
class Counter {
    private int count;
    void increment() { count++; }
    int get() { return count; }
}
```

Two threads can read the same old value and overwrite one another's update. The final result can be lower than the number of increments.

Protect it with a lock:

```java
class SafeCounter {
    private int count;
    synchronized void increment() { count++; }
    synchronized int get() { return count; }
}
```

Or use an atomic counter when the state is just one independent number.

## 3. Atomic classes and compare-and-set

Classes such as AtomicInteger, AtomicLong, AtomicBoolean and AtomicReference provide atomic operations. Many use compare-and-set (CAS): update the value only if it still equals the expected value.

```java
AtomicInteger count = new AtomicInteger();
count.incrementAndGet();
boolean changed = count.compareAndSet(0, 10);
```

CAS avoids a traditional lock for supported operations, but repeated contention can cause retries. Atomic operations do not automatically protect relationships across multiple variables.

## 4. LongAdder vs AtomicLong

LongAdder spreads updates across internal cells under contention and combines them when `sum()` is called. It can improve throughput for highly contended statistics such as request counters. It is not a drop-in replacement when you need an immediately exact atomic value for every read or compare-and-set operation.

Use AtomicLong for atomic updates and conditional operations on one value; consider LongAdder for high-contention statistics.

## 5. ABA problem

With CAS, a value can change A -> B -> A. A thread that checks only "is it still A?" may miss the intermediate change. This is the ABA problem. AtomicStampedReference can pair a reference with a version stamp to detect such changes. It is mainly relevant in low-level lock-free algorithms.

## 6. Deadlock

Deadlock occurs when threads wait forever for resources held by one another.

Example:
- Thread 1 holds lock A and waits for lock B.
- Thread 2 holds lock B and waits for lock A.

Prevention:
1. Acquire multiple locks in one consistent global order.
2. Keep critical sections short.
3. Avoid calling unknown callbacks or external services while holding locks.
4. Use timed tryLock where suitable.
5. Reduce nested locking and shared mutable state.

## 7. Other liveness problems

- **Deadlock:** involved threads cannot proceed because of circular waiting.
- **Starvation:** a thread repeatedly fails to obtain CPU time or a needed resource.
- **Livelock:** threads remain active and react to one another but make no useful progress.

## 8. Java Memory Model and visibility

A thread may not observe another thread's write unless there is a happens-before relationship. Important mechanisms include synchronized, volatile, Thread.start/join and concurrency utilities.

A volatile variable provides visibility/order for that variable; it does not make compound updates atomic. An AtomicInteger provides atomic operations on its value. A lock can protect a multi-field invariant.

## 9. Local locks vs distributed coordination

A Java lock protects threads within one JVM. If an application runs on five server instances, each instance has its own lock. It does not coordinate updates across all five.

For shared database state, use database transactions, constraints and suitable optimistic/pessimistic locking. For distributed coordination, choose a design appropriate to the failure model; do not assume a local synchronized block protects a cluster.

## 10. Common mistakes

- Assuming volatile makes increment atomic.
- Assuming AtomicInteger can protect several related fields.
- Using a local lock to protect shared data across multiple application instances.
- Acquiring multiple locks in inconsistent order.
- Using LongAdder where an exact atomic read/conditional update is required.
- Ignoring starvation and livelock while looking only for deadlock.

## 11. Interview questions

**Q1. What is a race condition?** A timing-dependent bug where concurrent operations access shared state and the result depends on their interleaving.

**Q2. AtomicInteger vs volatile int?** AtomicInteger supports atomic read-modify-write methods; volatile int gives visibility but increment is still multiple steps.

**Q3. What is CAS?** Compare-and-set updates a value only if it still matches an expected value.

**Q4. What is the ABA problem?** A value changes away from A and later back to A, so a CAS check sees A but misses the intermediate change.

**Q5. AtomicLong vs LongAdder?** AtomicLong provides atomic operations on one value; LongAdder can scale high-contention statistics but its sum is not an atomic snapshot for conditional updates.

**Q6. Does synchronized protect data across multiple Spring Boot instances?** No. It only coordinates threads using that lock in the same JVM.

**Q7. Deadlock vs starvation vs livelock?** Deadlock is circular waiting; starvation is lack of resource access; livelock is activity without progress.

## 12. Practice

1. Increment a counter from multiple threads using plain int, synchronized, AtomicInteger and LongAdder.
2. Demonstrate a two-lock deadlock and fix it with consistent lock ordering.
3. Explain why AtomicInteger is not enough for a transfer that updates two account balances.
4. Explain why synchronized in a multi-instance service does not protect a shared database row.
