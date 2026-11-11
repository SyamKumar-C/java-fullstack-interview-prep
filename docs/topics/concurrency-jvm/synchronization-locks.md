# Synchronization, Locks and Thread Coordination

## 1. Why synchronization?

When multiple threads access mutable shared data, operations can interleave and produce incorrect results. Synchronization protects critical sections and establishes memory-visibility guarantees.

## 2. synchronized

Every Java object can act as an intrinsic monitor lock. An instance synchronized method locks the current object; a static synchronized method locks the Class object.

```java
class Account {
    private int balance;
    public synchronized void deposit(int amount) {
        balance += amount;
    }
    public synchronized int balance() {
        return balance;
    }
}
```

All code that protects the same state must use the same lock. Synchronizing only one method does not protect unsynchronized access elsewhere.

## 3. synchronized block

A block lets you choose a lock and keep the critical section small.

```java
synchronized (lock) {
    // access shared state
}
```

Avoid locking on public objects, strings or boxed values because unrelated code may use the same monitor.

## 4. ReentrantLock

ReentrantLock provides explicit lock/unlock control, timed acquisition, interruptible acquisition and optional fairness.

```java
private final Lock lock = new ReentrantLock();

void update() {
    lock.lock();
    try {
        // critical section
    } finally {
        lock.unlock();
    }
}
```

Always unlock in finally. A lock is reentrant: the owning thread can acquire it again.

## 5. ReadWriteLock

A ReadWriteLock allows multiple readers or one writer. It can help when reads greatly outnumber writes and lock contention is measurable. It is not automatically faster; benchmark the workload.

## 6. volatile

Volatile guarantees visibility and ordering for reads/writes of a variable. It does not make compound operations atomic.

```java
private volatile boolean running = true;
```

This can work as a stop flag. It does not make `running++` safe.

## 7. wait, notify and notifyAll

These methods coordinate threads using an object's monitor. They must be called while owning that monitor. Always wait in a loop because wakeups can be spurious and the condition may no longer hold.

Modern code should usually prefer higher-level tools such as BlockingQueue, Semaphore, CountDownLatch or Condition.

## 8. Coordination utilities

- CountDownLatch: one or more threads wait until a count reaches zero; one-shot.
- CyclicBarrier: a group of threads wait for each other at a reusable barrier.
- Semaphore: limits concurrent access using permits.
- Condition: explicit wait sets associated with a Lock.

## 9. Practical scenarios

- Protect a shared in-memory map update with a lock.
- Use Semaphore to limit concurrent calls to a slow external service.
- Use CountDownLatch to wait for several startup tasks.
- Use BlockingQueue for producer-consumer processing.

## 10. Common mistakes

- Forgetting unlock in finally.
- Using different locks for the same shared state.
- Assuming volatile makes increment atomic.
- Calling wait/notify without owning the monitor.
- Holding a lock during slow network or database work.
- Choosing fairness without measuring its throughput cost.

## 11. Interview questions

**Q1. synchronized vs Lock?** synchronized is simpler and releases automatically; Lock provides timed/interruptible acquisition and other advanced controls.

**Q2. What is reentrancy?** A thread holding a lock can acquire the same lock again without deadlocking itself.

**Q3. volatile vs synchronized?** volatile provides visibility/order for a variable; synchronized also provides mutual exclusion for a critical section.

**Q4. CountDownLatch vs CyclicBarrier?** Latch is one-shot countdown; barrier lets a fixed group rendezvous and can be reused.

**Q5. Why use Semaphore?** To limit how many threads can access a resource concurrently.

## 12. Practice

Protect a shared bank balance with synchronized and ReentrantLock. Then use Semaphore to limit a simulated external API to five concurrent calls.
