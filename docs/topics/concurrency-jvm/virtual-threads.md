# Java Virtual Threads (Java 21)

## 1. Why virtual threads?

Platform threads are backed by operating-system threads. They are effective, but creating and blocking very large numbers of them consumes significant resources.

Virtual threads are lightweight threads managed by the JVM. They make a thread-per-task style practical for applications with many concurrent tasks that spend much of their time waiting, especially on I/O.

They improve scalability for many blocking tasks; they do not make CPU work execute faster or create more CPU cores.

## 2. Creating virtual threads

```java
Thread.startVirtualThread(() ->
    System.out.println(Thread.currentThread()));

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    Future<String> result = executor.submit(() -> callRemoteService());
    System.out.println(result.get());
}
```

Java 21 also provides `Thread.ofVirtual()` and the virtual-thread-per-task executor. The executor is designed to create a virtual thread for each task; do not pool virtual threads just to limit their count.

## 3. Platform vs virtual threads

| Platform thread | Virtual thread |
|---|---|
| Backed by an OS thread | Scheduled by JVM over carrier platform threads |
| More costly at very high counts | Much cheaper to create in large numbers |
| Useful for CPU work and general concurrency | Especially useful for many mostly-blocking tasks |
| Thread pools are common | Usually one virtual thread per task |

## 4. Scheduling and carrier threads

A virtual thread runs on a carrier platform thread while mounted. During supported blocking operations, the JVM can unmount the virtual thread so its carrier can run another virtual thread. When ready, it can be scheduled again.

The application still uses the familiar Thread API; the runtime manages the scheduling.

## 5. I/O-bound vs CPU-bound work

Virtual threads are useful when tasks spend much of their time waiting for HTTP, database or other I/O. If a task spends most of its time calculating, virtual threads do not increase CPU capacity. Use bounded parallelism for CPU-heavy processing.

Example: many incoming requests each wait for a remote service. Virtual threads can make the code simpler and reduce the cost of blocked threads, but the remote service still has a maximum safe request rate.

## 6. Pinning: Java 21 detail

A virtual thread can be pinned to its carrier during certain operations, preventing unmounting. In Java 21, blocking while executing inside a synchronized method or block is a notable pinning case. Native/foreign calls can also pin.

Avoid slow blocking operations while holding a monitor. Keep synchronized sections short. Pinning behavior and diagnostics evolve across JDK releases, so check the exact JDK version when troubleshooting.

## 7. Virtual threads do not remove resource limits

Virtual threads do not make database connections, HTTP connection pools, CPU, memory or downstream APIs unlimited.

If a connection pool has 30 connections, thousands of virtual threads may still wait for those 30 connections. Use semaphores, connection pools, timeouts, rate limits, bulkheads and bounded queues to protect scarce resources.

```java
Semaphore permits = new Semaphore(20);

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000; i++) {
        executor.submit(() -> {
            permits.acquire();
            try {
                callDownstream();
            } finally {
                permits.release();
            }
        });
    }
}
```

In production, also handle InterruptedException, cancellation, request deadlines and failures.

## 8. Spring Boot considerations

Supported Spring Boot versions can enable virtual-thread support with configuration such as `spring.threads.virtual.enabled=true`. Verify the exact Spring Boot and JDK versions and the execution model being used.

Do not assume this automatically changes every custom executor, scheduler, third-party library or reactive pipeline. Measure the actual application behavior and review resource limits.

## 9. Virtual threads vs reactive programming

Virtual threads allow blocking-style code with lightweight concurrency. Reactive programming models asynchronous data flows and back-pressure explicitly. They solve related but different problems and can coexist. Choose based on workload, libraries, latency needs and team understanding.

## 10. Common mistakes

- Using virtual threads to speed up CPU-heavy calculations.
- Launching unlimited calls against a limited database or API.
- Pooling virtual threads unnecessarily.
- Blocking inside long synchronized sections.
- Assuming every library blocking call unmounts in every JDK version.
- Ignoring cancellation, timeouts and back-pressure.

## 11. Interview questions

**Q1. What are virtual threads?** Lightweight threads managed by the JVM and scheduled over carrier platform threads.

**Q2. Do they make code faster?** Not necessarily. They improve scalability for many waiting tasks; they do not add CPU capacity.

**Q3. Should we pool virtual threads?** Usually no. Create one per task and limit access to scarce resources separately.

**Q4. What is a carrier thread?** The platform thread on which a virtual thread is mounted while it runs.

**Q5. What is pinning?** A virtual thread remains tied to its carrier during an operation that prevents unmounting, reducing scheduler flexibility.

**Q6. Are they suitable for database-heavy services?** They can simplify concurrency, but database connections and database capacity remain limited. Apply concurrency limits and timeouts.

**Q7. Virtual threads vs WebFlux?** Virtual threads support blocking-style request handling; WebFlux uses a non-blocking reactive model. The right choice depends on the full stack and workload.

## 12. Practice

Submit 1,000 tasks using a virtual-thread-per-task executor. Use a Semaphore to limit concurrent downstream calls to 20. Explain why the semaphore remains necessary.
