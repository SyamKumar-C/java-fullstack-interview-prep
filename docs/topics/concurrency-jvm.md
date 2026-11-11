# Concurrency and JVM

> **Priority:** P1 | **Level:** Intermediate to advanced | **Study time:** 3–4 hours

Concurrency is about managing multiple tasks whose execution overlaps. Parallelism means tasks execute at the same time on multiple processing units.

## 1. Process, thread, and thread lifecycle

A process has its own memory space. Threads within a process share heap memory but have their own stacks and execution state.

Java thread states include NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, and TERMINATED. RUNNABLE includes a thread that may be running or ready to run.

## 2. Runnable vs Callable

- Runnable.run() does not return a result and cannot declare checked exceptions.
- Callable.call() returns a result and can throw checked exceptions.
- ExecutorService can submit either; Callable submission returns a Future.

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
try {
    Future<Integer> future = pool.submit(() -> 20 + 22);
    System.out.println(future.get());
} finally {
    pool.shutdown();
}
```

## 3. synchronized, volatile, and atomic classes

- synchronized provides mutual exclusion and visibility through monitor locking.
- volatile provides visibility and ordering guarantees for reads/writes of that variable, but does not make compound operations such as count++ atomic.
- AtomicInteger and related classes provide atomic operations such as incrementAndGet using low-level concurrency support.

## 4. Race conditions and deadlocks

A race condition occurs when the result depends on unpredictable timing between threads accessing shared state.

A deadlock occurs when threads wait forever for locks held by each other. Reduce risk by acquiring locks in a consistent order, keeping lock scope small, and avoiding unnecessary nested locks.

## 5. ExecutorService and CompletableFuture

ExecutorService manages task execution and thread reuse. CompletableFuture supports asynchronous stages and composition.

```java
CompletableFuture<String> user =
    CompletableFuture.supplyAsync(() -> loadUser());

CompletableFuture<String> greeting =
    user.thenApply(name -> "Hello " + name);

System.out.println(greeting.join());
```

Use explicit executors for workloads that need isolation or different resource limits. Handle exceptional completion with exceptionally, handle, or whenComplete as appropriate.

## 6. Virtual threads (Java 21)

Virtual threads are lightweight threads managed by the Java runtime. They are useful for applications with many concurrent tasks that spend much of their time waiting, such as blocking I/O.

They do not make CPU-heavy work faster, and they do not remove the need to limit scarce resources such as database connections. Use a connection pool and other resource limits.

## 7. Java Memory Model

The Java Memory Model defines when writes by one thread become visible to another. Happens-before relationships are established by mechanisms such as monitor unlock/lock, volatile write/read, thread start, and successful thread join.

## 8. JVM memory areas

- Heap: objects and arrays; shared among threads.
- Java stacks: method frames and local variables; each thread has its own stack.
- Metaspace: class metadata in native memory.
- Program counter and native method stacks: per-thread execution support.

Garbage collection reclaims unreachable heap objects. A memory leak can still occur when objects are no longer useful but remain reachable through references.

## 9. Interview questions

??? question "Does volatile make count++ thread-safe?"
    No. count++ is a read-modify-write sequence. Use AtomicInteger or synchronization when multiple threads update a counter.

??? question "What is the difference between concurrency and parallelism?"
    Concurrency is about handling multiple tasks with overlapping progress. Parallelism is about executing multiple tasks at the same instant, usually on multiple cores.

??? question "When should virtual threads be used?"
    They are a good fit for high-concurrency workloads with blocking waits. They are not a way to accelerate CPU-bound work and do not increase database connection capacity.

??? question "What is a deadlock?"
    A deadlock is a state where two or more threads wait indefinitely for resources held by each other, so none can proceed.

??? question "What is a memory leak in Java if garbage collection exists?"
    Garbage collection removes unreachable objects. If an application unintentionally retains references to objects, they remain reachable and cannot be collected.

## 10. Practice

1. Implement a thread-safe counter using synchronized and AtomicInteger.
2. Submit Callable tasks to an ExecutorService and handle Future exceptions.
3. Combine two independent API calls with CompletableFuture.
4. Explain how you would size database connections when using virtual threads.
