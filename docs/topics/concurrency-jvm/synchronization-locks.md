# synchronized, volatile, Locks and Thread Coordination

## 1. Why synchronization is needed

Concurrency means tasks can overlap. When threads access shared mutable state, operations can interleave. Two separate questions matter:

- **Atomicity:** can another thread observe a partially completed operation?
- **Visibility/order:** when one thread writes, when is another thread guaranteed to see it?

The Java Memory Model defines the rules for these interactions.

## 2. synchronized and intrinsic monitors

Every Java object has an intrinsic monitor. A thread must own that monitor to enter a synchronized block or method.

- Instance synchronized method: locks `this`.
- Static synchronized method: locks the Class object.
- Synchronized block: locks the specified object.

```java
class Account {
    private int balance;

    public synchronized void deposit(int amount) {
        balance += amount;
    }

    public synchronized int getBalance() {
        return balance;
    }
}
```

All reads and writes that protect the same invariant must follow the same locking rule. A synchronized getter does not help if another method updates the field without that lock.

Use a private final lock object when you need a dedicated lock:

```java
private final Object lock = new Object();

void update() {
    synchronized (lock) {
        // short critical section
    }
}
```

Avoid locking on public objects, String literals or boxed values because unrelated code may share those objects.

## 3. Reentrancy and monitor behavior

Intrinsic monitors are reentrant: a thread that already owns a monitor can enter another synchronized method/block guarded by the same monitor. The monitor is released when the thread exits the synchronized region, including through an exception.

A synchronized lock is exclusive: only one thread at a time owns that monitor.

## 4. volatile: visibility, not compound atomicity

A volatile write happens-before a later read of the same variable. It provides visibility and ordering guarantees for that variable.

```java
private volatile boolean running = true;

void stop() {
    running = false;
}

void work() {
    while (running) {
        // do work
    }
}
```

But this is not safe:

```java
private volatile int count;
void increment() { count++; } // read + add + write; not atomic
```

Use AtomicInteger or a lock when an atomic read-modify-write operation is required.

## 5. happens-before essentials

A happens-before relationship guarantees visibility and ordering, not simply that one line "probably ran first".

Important examples:
- Exiting a synchronized block happens-before a later entry into the same monitor.
- Writing a volatile variable happens-before a later read of that variable.
- Calling `Thread.start()` happens-before actions in the started thread.
- Actions in a thread happen-before another thread successfully returns from `join()`.
- Properly completed task submission and retrieval through concurrency utilities establish documented memory-consistency effects.

Without a suitable happens-before relationship, do not assume another thread sees a write promptly.

## 6. ReentrantLock

ReentrantLock provides explicit locking features that intrinsic synchronized does not expose directly: `tryLock`, timed acquisition, interruptible acquisition, optional fairness, and multiple Condition objects.

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

Always release in finally. Use `lockInterruptibly()` or timed `tryLock` when cancellation or bounded waiting matters. Fairness can reduce throughput and is not a guarantee of perfect scheduling.

## 7. ReadWriteLock and StampedLock

ReadWriteLock allows multiple readers or one writer. It can help when reads greatly outnumber writes and lock contention is measured. It is not automatically faster.

StampedLock offers read, write and optimistic-read modes, but is not reentrant and requires careful stamp validation. Use it only when its performance benefit is measured and the code is well understood.

## 8. wait, notify and notifyAll

These Object methods coordinate threads using an object's monitor. The caller must own the monitor. `wait()` releases that monitor while waiting and reacquires it before returning. `notify()` wakes one waiter; `notifyAll()` wakes all waiters, which then compete to reacquire the monitor.

Always wait in a loop because wakeups can be spurious and the condition may no longer hold:

```java
synchronized (lock) {
    while (!conditionIsTrue()) {
        lock.wait();
    }
    // act when condition is true
}
```

For most application code, prefer higher-level tools such as BlockingQueue, Semaphore, CountDownLatch, CyclicBarrier or Condition.

## 9. Coordination utilities

| Tool | Purpose |
|---|---|
| CountDownLatch | Wait until a one-time count reaches zero |
| CyclicBarrier | A group of threads rendezvous; reusable |
| Semaphore | Limit concurrent access with permits |
| BlockingQueue | Producer-consumer handoff with blocking |
| Condition | Wait/signal sets associated with a Lock |
| Phaser | Flexible multi-phase coordination |

## 10. synchronized vs Lock

| synchronized | ReentrantLock |
|---|---|
| Simpler syntax | Explicit lock/unlock |
| Automatic release on block exit | Must unlock in finally |
| One intrinsic condition wait set | Multiple Condition objects |
| No timed try-acquire API | tryLock and timed/interruptible acquisition |
| JVM-managed monitor | Explicit Lock API |

Choose the simplest mechanism that meets the requirement.

## 11. Common mistakes

- Assuming volatile makes `count++` atomic.
- Using different locks to protect the same state.
- Holding a lock during a slow database or network call.
- Forgetting unlock in finally.
- Calling wait/notify without owning the monitor.
- Using if instead of while around wait.
- Assuming a lock coordinates multiple JVM instances or application servers.

## 12. Interview questions

**Q1. synchronized vs volatile?** synchronized provides mutual exclusion and visibility through monitor rules; volatile provides visibility/order for reads and writes of one variable, but not mutual exclusion.

**Q2. What is happens-before?** A Java Memory Model ordering rule that also guarantees visibility of actions before the relationship to actions after it.

**Q3. Is synchronized reentrant?** Yes. A thread can acquire the same monitor multiple times.

**Q4. What happens when wait is called?** The thread releases that object's monitor and waits; it must reacquire the monitor before returning.

**Q5. CountDownLatch vs CyclicBarrier?** A latch is a one-shot countdown; a barrier lets a group rendezvous and can be reused.

**Q6. When would you use Semaphore?** To limit concurrent access to a resource, such as allowing only 10 concurrent calls to an external service.

## 13. Practice

1. Implement a shared counter using synchronized and AtomicInteger.
2. Create a volatile stop flag and explain why it works.
3. Use a Semaphore to limit concurrent calls to five.
4. Explain the difference between wait and sleep.
5. Show why all accesses to a shared invariant must use the same lock.
