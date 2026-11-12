# Concurrency and JVM Interview Coverage

Use this checklist to test understanding. For each item, explain the behavior, write a small example, identify a failure mode, and describe when you would use it.

## Threads and Java Memory Model
- [ ] Process vs thread; platform thread lifecycle and states
- [ ] Runnable vs Callable
- [ ] Race condition, atomicity, visibility and ordering
- [ ] Java Memory Model and happens-before
- [ ] Thread.start(), join(), synchronized and volatile visibility rules
- [ ] Thread interruption and cooperative cancellation
- [ ] wait, notify, notifyAll and condition loops
- [ ] Thread confinement and immutability

## Synchronization and coordination
- [ ] Intrinsic monitor; instance vs static synchronized
- [ ] Synchronized block and lock object choice
- [ ] Reentrancy
- [ ] volatile visibility vs atomic compound operations
- [ ] ReentrantLock: tryLock, timed and interruptible locking, fairness
- [ ] ReadWriteLock and StampedLock trade-offs
- [ ] CountDownLatch, CyclicBarrier, Semaphore, BlockingQueue, Condition and Phaser
- [ ] Lock ordering and deadlock prevention

## Atomicity and liveness
- [ ] AtomicInteger, AtomicLong, AtomicReference and CAS
- [ ] ABA problem and AtomicStampedReference
- [ ] AtomicLong vs LongAdder
- [ ] Deadlock vs starvation vs livelock
- [ ] Why local JVM locks do not coordinate multiple application instances
- [ ] Database transactions/locking for shared data across instances

## Executors and asynchronous programming
- [ ] Executor vs ExecutorService; execute vs submit
- [ ] ThreadPoolExecutor: core/max size, queue, keep-alive and rejection policy
- [ ] Bounded vs unbounded queues and overload protection
- [ ] shutdown, shutdownNow, awaitTermination and interruption
- [ ] Future: get, timeout, cancellation and isDone
- [ ] CompletableFuture: runAsync, supplyAsync and join
- [ ] thenApply vs thenCompose vs thenCombine; allOf
- [ ] exceptionally vs handle vs whenComplete
- [ ] Custom executor and common ForkJoinPool
- [ ] Timeouts, cancellation and downstream capacity

## Virtual threads
- [ ] Platform vs virtual threads, carrier threads and mount/unmount
- [ ] Thread-per-task executor
- [ ] I/O-bound vs CPU-bound workloads
- [ ] Java 21 pinning and synchronized blocking
- [ ] Why virtual threads do not remove database/API limits
- [ ] Semaphores, timeouts and back-pressure with virtual threads
- [ ] Spring Boot version and execution-model considerations

## JVM memory and garbage collection
- [ ] Heap, Java stack, PC register, native stack, metaspace and code cache
- [ ] Stack vs heap; StackOverflowError vs OutOfMemoryError
- [ ] GC roots and object reachability
- [ ] Young/old generation terminology and collector-specific details
- [ ] Stop-the-world pauses, throughput and latency
- [ ] Serial, Parallel, G1, ZGC and Shenandoah at a high level
- [ ] Heap, metaspace, direct memory, thread stack and container limits
- [ ] Memory leaks through static collections, caches, listeners, ThreadLocal and queues
- [ ] Heap dump, thread dump, GC logs, JFR and paths to GC roots
- [ ] JVM options: Xms, Xmx, Xss, MaxMetaspaceSize and GC logging

A topic is ready only when you can explain the rule, predict a short code example, identify a common bug, and justify the right tool for a real Spring Boot service.
