# ExecutorService, Callable, Future and CompletableFuture

## 1. Why executors?

Creating a new Thread for every task is expensive and difficult to manage. ExecutorService separates task submission from thread management and can reuse worker threads.

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
try {
    pool.submit(() -> System.out.println("Working"));
} finally {
    pool.shutdown();
}
```

In production, choose pool size and queue behavior based on workload. A fixed thread pool created by the convenience factory uses an unbounded queue, which can allow memory growth under overload.

## 2. Runnable vs Callable

- Runnable: no return value; cannot declare checked exceptions in `run()`.
- Callable<T>: returns a value and can throw checked exceptions.

```java
Callable<Integer> task = () -> 10 + 20;
Future<Integer> future = pool.submit(task);
Integer result = future.get();
```

## 3. Future

Future represents a result that may become available later. `get()` blocks until completion; `get(timeout, unit)` bounds the wait. It also supports cancellation and completion checks.

A Future does not provide rich composition. Calling get too early can block the current thread.

## 4. CompletableFuture

CompletableFuture supports asynchronous pipelines and composition.

```java
CompletableFuture<User> userFuture =
    CompletableFuture.supplyAsync(() -> userService.loadUser(10), pool);

CompletableFuture<String> nameFuture =
    userFuture.thenApply(User::getName);

String name = nameFuture.join();
```

Important methods:
- `thenApply`: transform a result synchronously in the completion chain.
- `thenApplyAsync`: transform asynchronously using an executor/default async facility.
- `thenCompose`: chain a function that returns another CompletionStage; avoids nested futures.
- `thenCombine`: combine two independent results.
- `allOf`: wait for several futures.
- `exceptionally`, `handle`, `whenComplete`: different error/observation handling.

## 5. thenApply vs thenCompose

If a function returns a plain value, use thenApply. If it returns another future, use thenCompose to flatten the result.

```java
CompletableFuture<User> user = loadUserAsync(1);
CompletableFuture<List<Order>> orders =
    user.thenCompose(u -> loadOrdersAsync(u.getId()));
```

## 6. Error handling

`exceptionally` can recover with a fallback value. `handle` receives either result or error and can transform either. `whenComplete` observes completion but normally preserves the original result/failure.

Do not silently convert every failure into an empty result if the caller needs to know the operation failed.

## 7. Executor choice

CPU-bound tasks need a pool sized around available processors. Blocking I/O tasks may need more concurrency, a dedicated pool or virtual threads, depending on the application. Avoid using the common ForkJoinPool blindly for blocking work.

## 8. Practical scenario

A dashboard loads profile, notifications and order summary independently. Start all three calls, combine their results, and apply a timeout/fallback policy to each call. This reduces sequential waiting, but the downstream services still need capacity controls.

## 9. Common mistakes

- Forgetting shutdown/ownership of an ExecutorService.
- Using an unbounded queue without overload protection.
- Blocking with get() when composition is possible.
- Using thenApply where thenCompose is required.
- Ignoring exceptions inside asynchronous stages.
- Running blocking tasks on a shared CPU-oriented pool.

## 10. Interview questions

**Q1. Runnable vs Callable?** Runnable has no result; Callable returns a result and can throw checked exceptions.

**Q2. Future vs CompletableFuture?** Future represents a pending result; CompletableFuture also supports composition, callbacks and explicit completion.

**Q3. thenApply vs thenCompose?** thenApply maps a value; thenCompose chains another asynchronous operation and flattens the result.

**Q4. What does get() do?** It blocks until the result is ready or an exception/timeout occurs.

**Q5. Why pass a custom executor?** To control thread usage and isolate workloads from shared pools.

## 11. Practice

Run two independent service calls with CompletableFuture, combine their results with thenCombine, and handle one failure without hiding the other result.
