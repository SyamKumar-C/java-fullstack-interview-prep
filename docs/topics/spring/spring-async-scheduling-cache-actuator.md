# Spring Async, Scheduling, Caching and Actuator

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Short interview answer
Spring supports asynchronous method execution, scheduled jobs, caching, and operational endpoints. These features commonly use Spring proxies, so self-invocation can bypass them. Thread pools, cache invalidation, duplicate job execution, and endpoint security must be designed.

## 2. Async execution

```java
@Configuration
@EnableAsync
class AsyncConfig {}

@Service
class ReportService {
    @Async("reportExecutor")
    public CompletableFuture<Report> generate(long id) {
        return CompletableFuture.completedFuture(buildReport(id));
    }
}
```

Configure a bounded pool:

```java
@Bean("reportExecutor")
Executor reportExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(4);
    executor.setMaxPoolSize(8);
    executor.setQueueCapacity(100);
    executor.setThreadNamePrefix("report-");
    executor.initialize();
    return executor;
}
```

A bounded queue protects the process from unlimited task buildup. Define what happens when it is full. A void async method cannot return its failure to the caller; use a Future/CompletableFuture or configure an async exception handler. Thread-local security or tenant context does not automatically transfer to every executor.

A call such as `this.generate()` normally bypasses the proxy. Put the async method on another Spring bean or use an appropriate proxy boundary.

## 3. Scheduling

```java
@EnableScheduling
@Configuration
class ScheduleConfig {}

@Component
class CleanupJob {
    @Scheduled(cron = "0 0 2 * * *", zone = "Asia/Kolkata")
    void cleanExpiredRecords() {
        // process a bounded batch
    }
}
```

Spring cron has six fields: second, minute, hour, day of month, month, day of week. Every application replica may run the schedule. Use distributed locking or a single scheduler if duplicate execution is unsafe.

## 4. Caching

```java
@EnableCaching
@Configuration
class CacheConfig {}

@Service
class ProductService {
    @Cacheable(cacheNames = "products", key = "#id")
    public ProductDto find(long id) {
        return loadFromDatabase(id);
    }

    @CacheEvict(cacheNames = "products", key = "#id")
    public void update(long id, UpdateProduct request) {
        updateDatabase(id, request);
    }
}
```

- Cacheable: return cached value on a hit; otherwise run and cache.
- CachePut: always run and update the cache.
- CacheEvict: remove entries.
- unless: decide after execution whether to cache the result.

Choose a provider such as Caffeine (local) or Redis (shared). Define TTL, size limits, keys, serialization, invalidation, and failure behavior. Include tenant/user identity in keys when results are tenant-specific. Local caches on multiple pods can become inconsistent.

## 5. Actuator
Actuator exposes health, info, metrics, and other operational endpoints. Expose only what is needed. Secure management endpoints and restrict network access. Do not expose environment, configuration, heap dump, or sensitive endpoints publicly. Liveness means the process can continue; readiness means it can receive traffic.

## 6. Common mistakes
- Self-invoking async/cache/transaction methods.
- Using an unbounded executor queue.
- Running destructive scheduled work on every replica.
- Using incomplete cache keys.
- Evicting before a transaction commits and then repopulating stale data.
- Exposing Actuator endpoints publicly.

## 7. Interview questions
**Q1. Does Async create a new thread each time?** It submits work to a configured executor, often a pool.

**Q2. Why might Async not work?** Missing enablement, non-managed object, self-invocation, or executor problems.

**Q3. Cacheable vs CachePut?** Cacheable may skip method execution on hit; CachePut always executes and updates the cache.

**Q4. What happens with three replicas and one scheduled method?** By default, all three may run it.

**Q5. What is Actuator for?** Health and operational visibility, with secured endpoint exposure.

## 8. Practice
1. Configure a bounded executor and observe rejected tasks.
2. Run two replicas and test duplicate scheduling.
3. Add cache eviction to an update API.
4. Design a tenant-aware cache key.
