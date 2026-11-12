# Spring Async, Scheduling, Caching and Actuator

## 1. Short interview answer
Spring provides method-level async execution, scheduled jobs, caching and operational endpoints. Several of these features use proxies. Production design must consider bounded thread pools, failure handling, duplicate schedules across replicas, cache invalidation and endpoint security.

## 2. Async execution with @Async
Enable async processing and use a Spring-managed bean:

```java
@Configuration
@EnableAsync
class AsyncConfiguration {}

@Service
class ReportService {
    @Async("reportExecutor")
    public CompletableFuture<Report> generate(long id) {
        Report report = buildReport(id);
        return CompletableFuture.completedFuture(report);
    }
}
```

Configure a bounded executor:

```java
@Bean("reportExecutor")
ThreadPoolTaskExecutor reportExecutor() {
    var executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(4);
    executor.setMaxPoolSize(8);
    executor.setQueueCapacity(100);
    executor.setThreadNamePrefix("report-");
    executor.initialize();
    return executor;
}
```

A bounded queue prevents unlimited task buildup. Choose a rejection policy and monitor queue depth, active threads and task duration. A void-returning async method cannot deliver its exception through a Future; use `CompletableFuture` when the caller needs completion/failure information or configure an async uncaught-exception handler.

### Important async traps
- Self-invocation normally bypasses the proxy.
- Calling an async method on an object created with `new` bypasses Spring.
- ThreadLocal values (security context, tenant context, MDC) do not automatically propagate to every executor.
- Do not assume submitting work makes it durable; a process crash can lose in-memory tasks.
- Do not block request threads waiting for async work if the goal was to reduce request latency.

## 3. CompletableFuture composition
Use `thenApply` to transform a result, `thenCompose` to chain another asynchronous operation, and `thenCombine` to combine independent results. Handle failures with `exceptionally`, `handle` or `whenComplete`. Decide which executor runs CPU-heavy continuations rather than unintentionally using the common pool.

## 4. Scheduling
```java
@Configuration
@EnableScheduling
class SchedulingConfiguration {}

@Component
class CleanupJob {
    @Scheduled(cron = "0 0 2 * * *", zone = "Asia/Kolkata")
    void cleanExpiredRecords() {
        // process a bounded batch
    }
}
```

Spring cron has six fields: second, minute, hour, day-of-month, month, day-of-week. Fixed delay is measured from completion to the next start; fixed rate targets a regular interval. A long-running job and scheduler configuration can affect overlap behavior.

**Multiple replicas:** each application instance can run the same scheduled method. If only one execution is allowed, use a distributed lock (for example, a database-backed lock), a dedicated scheduler/worker, or an external job platform. Make the job idempotent because retries and restarts can still repeat work.

## 5. Caching annotations
```java
@EnableCaching
@Configuration
class CacheConfiguration {}

@Service
class ProductService {
    @Cacheable(cacheNames = "products", key = "#id")
    public ProductDto find(long id) {
        return loadFromDatabase(id);
    }

    @CachePut(cacheNames = "products", key = "#id")
    public ProductDto refresh(long id) {
        return loadFromDatabase(id);
    }

    @CacheEvict(cacheNames = "products", key = "#id")
    public void update(long id, UpdateProduct request) {
        updateDatabase(id, request);
    }
}
```

- `@Cacheable`: returns a cached value on hit; otherwise invokes the method and stores the result.
- `@CachePut`: always invokes the method and stores its result.
- `@CacheEvict`: removes entries.
- `allEntries = true`: clears all entries in that cache.
- `unless`: prevents storing a result after method execution when the expression is true.
- `condition`: decides whether caching applies before invocation.

Cache keys must include every dimension that changes the result, such as tenant ID, locale, permissions or filter parameters. Do not cache sensitive user-specific data under a shared key.

### Cache invalidation and consistency
Update the database and invalidate/update the cache in a deliberate order. A process-local cache is not automatically shared across replicas. For distributed caches, define TTL, eviction, serialization, failure behavior and invalidation strategy. Self-invocation can bypass cache advice.

## 6. Actuator
Spring Boot Actuator provides operational endpoints such as health, info, metrics, environment and conditions (availability depends on dependencies/configuration). Expose only endpoints needed by operators.

- Keep sensitive endpoints off the public internet.
- Secure management endpoints with authentication/network controls.
- Avoid exposing environment/configuration values that may contain secrets.
- Add custom health indicators for important dependencies, but do not make health checks so expensive that they overload the system.
- Distinguish liveness (process can continue) from readiness (instance can accept traffic).

## 7. Production scenario
A report API accepts a request and queues report generation. Use a bounded executor for short-lived in-process work, persist a job record if work must survive restarts, and expose job status. A scheduled cleanup should be idempotent and coordinated across replicas. Cache frequently read reference data with tenant-aware keys and clear it when master data changes.

## 8. Common mistakes
- Unbounded executor queues and no rejection strategy.
- Assuming async work is durable.
- Assuming scheduled methods run only once across a cluster.
- Using cache keys that omit tenant/user/filter context.
- Expecting self-invocation to trigger async/cache advice.
- Exposing all Actuator endpoints publicly.
- Treating a cache as the source of truth.

## 9. Interview questions
**Q1. How does @Async work?** Spring advice submits the method invocation to a configured executor.

**Q2. Why might @Async not work?** Missing enablement, non-managed object, self-invocation, wrong executor or proxy limitations.

**Q3. @Cacheable vs @CachePut?** Cacheable can skip method execution on a hit; CachePut always executes and updates the cache.

**Q4. What happens with three replicas and one @Scheduled method?** Each replica may run it unless coordination is added.

**Q5. Fixed delay vs fixed rate?** Fixed delay waits after completion; fixed rate targets a regular start interval.

**Q6. What is Actuator?** Operational endpoints for health and application metrics/diagnostics; exposure must be controlled.

## Practice
1. Configure a bounded executor and test what happens when its queue is full.
2. Demonstrate self-invocation and external invocation for an async method.
3. Run two replicas and verify whether a scheduled job executes twice.
4. Design cache keys for a multi-tenant product API.
5. Expose health/readiness while protecting sensitive management endpoints.
