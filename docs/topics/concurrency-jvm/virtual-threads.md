# Virtual Threads (Java 21)

Virtual threads are lightweight threads managed by the JVM. They make thread-per-task designs practical for high-concurrency, mostly blocking workloads.

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
  Future<String> a = executor.submit(() -> callServiceA());
  Future<String> b = executor.submit(() -> callServiceB());
  System.out.println(a.get() + b.get());
}
```

They reduce the cost of waiting tasks; they do not make CPU-heavy work faster or remove limits on databases, APIs, file descriptors or memory.

Use one virtual thread per task, not a pool of virtual threads. Protect scarce downstream resources with semaphores/connection pools. Understand pinning behavior for the JDK version in use. ThreadLocal values still exist and can be costly at very high thread counts.

**Interview:** Do virtual threads replace reactive programming? Not universally; they simplify blocking code but do not remove capacity and architecture concerns.
