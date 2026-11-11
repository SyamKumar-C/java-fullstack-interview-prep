# ExecutorService, Future and CompletableFuture

ExecutorService separates task submission from thread management.

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
try {
  Future<Integer> future = pool.submit(() -> 20 + 22);
  System.out.println(future.get()); // blocks until result
} finally { pool.shutdown(); }
```

Future represents a result that may become available. get() blocks; cancel() requests cancellation, which is cooperative.

CompletableFuture supports composition: thenApply transforms, thenCompose chains another stage, thenCombine combines independent stages, allOf waits for all, exceptionally/handle process failures.

```java
CompletableFuture<String> result =
  CompletableFuture.supplyAsync(() -> "order").thenApply(String::toUpperCase);
```

Async methods without an explicit executor commonly use the common pool. Use a suitable executor for application workloads.

**Interview:** thenApply vs thenCompose? Transform a value vs flatten a stage returned by another async operation.
