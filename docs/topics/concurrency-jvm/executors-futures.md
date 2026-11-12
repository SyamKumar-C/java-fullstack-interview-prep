# ExecutorService, Future and CompletableFuture

## 1. Why use executors?

Creating a new platform Thread for every request makes thread creation, cleanup, limits and failure handling difficult. An executor separates task submission from the mechanism that runs tasks.

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
try {
    Future<Integer> result = pool.submit(() -> 10 + 20);
    System.out.println(result.get());
} finally {
    pool.shutdown();
}
```

The example waits for the task with `get()`. In a real application, do not block a request thread unnecessarily; compose asynchronous work when possible.

## 2. Runnable vs Callable

| Runnable | Callable<T> |
|---|---|
| `run()` returns void | `call()` returns T |
| Cannot declare checked exceptions | Can throw checked exceptions |
| Used with execute or submit | Usually submitted to get a Future<T> |

```java
Runnable logTask = () -> System.out.println("Started");
Callable<String> loadTask = () -> "loaded";
Future<String> future = pool.submit(loadTask);
```

## 3. ExecutorService lifecycle

- `execute(task)`: submit a Runnable without a result.
- `submit(task)`: submit Runnable/Callable and get a Future.
- `shutdown()`: stop accepting new tasks; finish submitted tasks.
- `shutdownNow()`: request interruption of running tasks and return tasks that never started. It is best-effort, not a guarantee that tasks stop.
- `awaitTermination(...)`: wait for termination for a bounded time.

Interruption is cooperative. Tasks should respond to interruption and restore the interrupt flag when they cannot propagate InterruptedException.

```java
pool.shutdown();
try {
    if (!pool.awaitTermination(10, TimeUnit.SECONDS)) {
        pool.shutdownNow();
    }
} catch (InterruptedException ex) {
    pool.shutdownNow();
    Thread.currentThread().interrupt();
}
```

## 4. Thread pools and queueing

A pool controls how many tasks run at once. Important choices include core size, maximum size, keep-alive time, work queue and rejection policy.

Be careful with convenience factories: `Executors.newFixedThreadPool(n)` uses an unbounded queue. Under sustained overload, queued tasks can consume large amounts of memory. Production systems should choose queue capacity and rejection behavior deliberately, often using a configured ThreadPoolExecutor.

Common rejection policies include AbortPolicy (throws), CallerRunsPolicy (submitting thread runs task), DiscardPolicy and DiscardOldestPolicy. Choose based on whether slowing the producer, rejecting work or dropping work is acceptable.

## 5. Future

Future represents a result that may be available later.

- `get()` blocks until completion.
- `get(timeout, unit)` bounds the wait.
- `isDone()` checks completion.
- `cancel(true)` requests cancellation and may interrupt the running task.
- `isCancelled()` checks cancellation.

A Future is useful for retrieving one result, but does not offer rich dependent-stage composition. Calling get too early can block.

## 6. CompletableFuture basics

CompletableFuture implements CompletionStage and supports callbacks, transformations and composition.

```java
CompletableFuture<User> userFuture =
    CompletableFuture.supplyAsync(() -> userService.loadUser(10), ioExecutor);

CompletableFuture<String> nameFuture =
    userFuture.thenApply(User::getName);

String name = nameFuture.join();
```

- `runAsync(Runnable)`: async work with no result.
- `supplyAsync(Supplier)`: async work that returns a result.
- Without an explicit executor, async methods commonly use the common ForkJoinPool.
- Non-async continuation methods may run on the thread that completes the previous stage or another thread participating in completion. Do not assume a particular thread.

Pass a suitable executor for blocking I/O work rather than using the common pool blindly.

## 7. thenApply vs thenCompose vs thenCombine

`thenApply` transforms a value. `thenCompose` chains a function that itself returns a future, avoiding nested futures. `thenCombine` combines two independent future results.

```java
CompletableFuture<User> user = loadUserAsync(10);

CompletableFuture<List<Order>> orders =
    user.thenCompose(u -> loadOrdersAsync(u.getId()));

CompletableFuture<Dashboard> dashboard =
    user.thenCombine(loadNotificationsAsync(10),
        (u, notifications) -> new Dashboard(u, notifications));
```

## 8. allOf and collecting results

`allOf` completes when all supplied futures complete. It returns CompletableFuture<Void>; it does not automatically give you a list of their results.

```java
CompletableFuture<User> user = loadUserAsync(1);
CompletableFuture<List<Order>> orders = loadOrdersAsync(1);

CompletableFuture<Void> all =
    CompletableFuture.allOf(user, orders);

CompletableFuture<Dashboard> dashboard =
    all.thenApply(ignored ->
        new Dashboard(user.join(), orders.join()));
```

If one future fails, the combined stage also completes exceptionally. Decide whether all results are required or whether partial results are acceptable.

## 9. Exception handling

- `exceptionally(fn)`: recover from an exception with a fallback result.
- `handle((value, error) -> ...)`: transform success or failure into a result.
- `whenComplete((value, error) -> ...)`: observe completion; normally preserves the original outcome.

```java
CompletableFuture<User> safeUser =
    loadUserAsync(1)
        .exceptionally(error -> fallbackUser());
```

Do not silently turn every error into an empty or default value if the caller must know the operation failed. Log useful context and preserve the cause.

## 10. Timeout and cancellation

Use `orTimeout` to complete a future exceptionally after a time limit, or `completeOnTimeout` to complete it with a fallback (Java 9+). These methods do not guarantee that the underlying external operation has physically stopped. Configure timeouts in the HTTP/database client as well.

Cancellation and interruption are cooperative; design tasks and clients to respond to them.

## 11. Choosing a concurrency model

- CPU-bound tasks: bounded pool sized around available processors, then measure.
- Blocking I/O: dedicated bounded executor or virtual threads, with downstream limits.
- Independent async operations: CompletableFuture composition.
- Producer/consumer pipelines: bounded queues and explicit back-pressure.
- Do not create an unbounded queue or unbounded downstream concurrency by accident.

## 12. Practical Spring service scenario

A dashboard needs profile, order summary and notifications. Start independent calls concurrently and combine results. Give each dependency a timeout, isolate blocking calls from CPU work, and decide what to do if one dependency fails. This can reduce total waiting time, but does not increase the capacity of the downstream services.

## 13. Common mistakes

1. Forgetting to shut down an executor that the application owns.
2. Assuming shutdownNow forcibly kills tasks.
3. Using an unbounded queue without overload protection.
4. Calling Future.get too early and blocking.
5. Using thenApply when thenCompose is required.
6. Assuming allOf returns the individual results.
7. Ignoring exceptional completion.
8. Running blocking I/O on a shared CPU-oriented pool.
9. Assuming timeout always cancels the underlying network operation.

## 14. Interview questions

**Q1. Runnable vs Callable?** Runnable returns no result; Callable returns a value and can throw checked exceptions.

**Q2. execute vs submit?** execute accepts a Runnable and returns no Future; submit returns a Future for completion/result tracking.

**Q3. Future vs CompletableFuture?** Future represents a pending result; CompletableFuture also supports completion stages, composition and callbacks.

**Q4. thenApply vs thenCompose?** thenApply maps a value; thenCompose chains a future-returning function and flattens the result.

**Q5. What does shutdown do?** It rejects new tasks but allows submitted tasks to finish.

**Q6. Why pass a custom executor?** To control resource use and isolate blocking, CPU and latency-sensitive workloads.

**Q7. Does CompletableFuture make a task asynchronous automatically?** No. Async methods schedule work; a continuation may run on a completion thread. The execution method and executor matter.

## 15. Practice

1. Submit a Callable and read its Future with a timeout.
2. Combine two independent service calls with thenCombine.
3. Chain user lookup to order lookup with thenCompose.
4. Compare exceptionally, handle and whenComplete.
5. Build a ThreadPoolExecutor with a bounded queue and explain its rejection policy.
