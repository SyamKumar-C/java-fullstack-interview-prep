# synchronized, volatile and Locks

## synchronized
An instance synchronized method locks the object's monitor; a static synchronized method locks the Class object. Locks are reentrant. Unlock happens-before a later lock on the same monitor, providing mutual exclusion and visibility.

## volatile
A volatile write happens-before a later read of that variable. It provides visibility and ordering, but not atomicity for compound operations.

```java
volatile boolean running = true;
// visible across threads; count++ would still not be atomic
```

## Lock API
ReentrantLock supports explicit lock/unlock, timed and interruptible acquisition and optional fairness.
```java
lock.lock();
try { updateState(); }
finally { lock.unlock(); }
```

Always unlock in finally. ReadWriteLock may help read-heavy workloads; measure first.

**Interview:** volatile vs synchronized? Visibility/order for a variable vs mutual exclusion plus visibility for a critical section. Is volatile int++ atomic? No.
