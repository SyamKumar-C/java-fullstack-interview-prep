# Java Virtual Threads (Java 21)

## 1. The problem virtual threads solve

A platform thread is backed by an operating-system thread. Threads are useful for concurrency, but large numbers of blocked platform threads consume memory and operating-system resources.

Virtual threads are lightweight threads managed by the Java runtime. They make a thread-per-task programming style practical for applications with many concurrent tasks that spend much of their time waiting, especially on I/O.

They improve scalability for blocking workloads; they do not make CPU work execute faster.

## 2. Creating virtual threads

Java 21 provides several APIs:

```java
Thread.startVirtualThread(() -> {
    System.out.println(Thread.currentThread());
});

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    Future<String> result = executor.submit(() -> callRemoteService());
    System.out.println(result.get());
}
```

The executor creates a new virtual thread for each submitted task. Do not pool virtual threads just to limit their count; use a semaphore or other resource limit to protect a constrained downstream system.

## 3. Platform thread versus virtual thread

| Platform thread | Virtual thread |
|---|---|
| OS-thread-backed | Scheduled by JVM over carrier platform threads |
| More expensive to create and block at large scale | Cheap to create in large numbers |
| Useful for CPU work and general concurrency | Best for many mostly-blocking tasks |
| Pooling is common | Usually one virtual thread per task |

## 4. How scheduling works

A virtual thread runs on a carrier platform thread. When it performs supported blocking operations, the runtime can unmount it from the carrier so that the carrier can run another virtual thread. When the operation is ready, the virtual thread can be scheduled again.

The application still sees a normal Thread API. The runtime manages the scheduling.

## 5. Blocking I/O and CPU-bound work

Virtual threads are particularly useful for code that waits on network, database, or file I/O. If a task spends most of its time calculating, virtual threads do not create extra CPU cores. Use bounded parallelism for CPU-heavy work.

Example: 10,000 requests waiting on downstream HTTP calls may be easier to model with virtual threads than 10,000 platform threads. But the downstream service still has a capacity limit.

## 6. Pinning and synchronization

A virtual thread can be pinned to its carrier in certain situations, preventing unmounting during a blocking operation. In Java 21, a notable case is blocking while inside a `synchronized` region or method. Native/foreign calls can also pin.

Keep synchronized sections short and avoid blocking I/O while holding a monitor. JDK behavior evolves; check the target JDK's documentation and diagnostics when investigating pinning.

## 7. Back-pressure and resource limits

Virtual threads do not make databases, connection pools, APIs, or rate limits unlimited. If a database pool has 30 connections, thousands of virtual threads may still wait for those 30 connections.

Use semaphores, bounded queues, connection pools, timeouts, bulkheads, and rate limits to protect external resources.

## 8. Spring Boot scenario

Spring Boot applications on a supported JDK can enable virtual threads with the relevant Spring Boot configuration (for example, `spring.threads.virtual.enabled=true` in supported Boot versions). Verify the exact Spring Boot version and execution model. Not every framework task or third-party library automatically becomes virtual-thread-friendly.

Do not assume enabling virtual threads automatically changes every thread pool, async executor, scheduler, or reactive pipeline.

## 9. Common mistakes

- Using virtual threads to speed up CPU-bound calculations.
- Creating an unbounded number of downstream database requests.
- Pooling virtual threads unnecessarily.
- Blocking inside long synchronized sections.
- Assuming every blocking library operation unmounts cleanly.
- Ignoring timeouts, cancellation, and back-pressure.

## 10. Interview questions and answers

**Q1. What are virtual threads?**  
Lightweight threads managed by the JVM and scheduled over carrier platform threads. They support a simple thread-per-task style at high concurrency.

**Q2. Do virtual threads make code execute faster?**  
Not necessarily. They improve throughput and resource use for workloads with many waiting tasks. They do not add CPU capacity.

**Q3. Should we pool virtual threads?**  
Usually no. Create one per task. Limit access to scarce resources separately.

**Q4. What is a carrier thread?**  
The platform thread on which a virtual thread is mounted while it executes.

**Q5. What is pinning?**  
A virtual thread remains tied to its carrier during an operation that prevents unmounting, reducing scheduler flexibility.

**Q6. Are virtual threads suitable for database-heavy services?**  
They can simplify concurrency, but the connection pool and database remain bounded. Apply limits and timeouts so virtual threads do not overwhelm the database.

**Q7. Virtual threads versus reactive programming?**  
Virtual threads allow blocking-style code with lightweight concurrency. Reactive programming models asynchronous streams and back-pressure explicitly. Choose based on workload, libraries, and team needs; they can coexist.

## 11. Practice exercise

Write a program that submits 1,000 tasks using a virtual-thread-per-task executor. Add a semaphore limiting concurrent calls to a simulated downstream service to 20. Explain why the semaphore is still needed.

## 12. Quick revision

Virtual threads make blocking concurrency cheaper, not CPU work faster. Protect databases and remote services with limits; avoid blocking while pinned.
