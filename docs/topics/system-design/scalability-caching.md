# Scalability, Caching and Load Balancing

## 1. Scalability

Vertical scaling adds resources to one machine. Horizontal scaling adds more instances. Horizontal scaling improves capacity and resilience but requires statelessness or shared state, load balancing and coordination.

## 2. Load balancing

A load balancer distributes requests across healthy instances. Common approaches include round robin, least connections and weighted routing. Health checks remove unhealthy instances from rotation.

Session affinity can help legacy stateful apps but reduces flexibility; shared session storage or stateless tokens are often better.

## 3. Caching layers

- Browser cache: static assets and client data.
- CDN: cache content near users.
- Application cache: in-process cache per instance.
- Distributed cache: shared cache such as Redis.
- Database cache/buffer: database-managed pages and plans.

Each layer has different latency, consistency and invalidation behavior.

## 4. Cache-aside pattern

1. Application reads cache.
2. If hit, return cached value.
3. If miss, read database.
4. Store result with TTL.
5. Return result.

The application controls cache population and invalidation.

## 5. Cache invalidation

Strategies include TTL expiry, explicit delete/update, versioned keys and event-based invalidation. Decide how stale data is acceptable and what happens if invalidation fails.

## 6. Cache problems

- Cache stampede: many requests reload the same missing/expired key.
- Thundering herd: many clients hit a dependency simultaneously.
- Hot key: one key receives disproportionate traffic.
- Stale data: cache is older than source of truth.
- Cache penetration: repeated requests for missing keys.

Mitigations include request coalescing, jittered TTLs, negative caching, rate limiting and sharding/hot-key strategies.

## 7. CAP and consistency trade-offs

During a network partition, a distributed system cannot guarantee both full availability and strong consistency for every operation. Real systems choose behavior per operation; CAP is not a simple permanent label for a database.

## 8. Practical scenario

A Spring Boot service has multiple replicas and caches tenant master data. An in-memory cache on each replica can become inconsistent. Use a shared cache or publish invalidation events, include tenant in cache keys and define TTL/fallback behavior.

## 9. Common mistakes

- Caching without an invalidation plan.
- Using local cache across multiple instances as if it were shared.
- Forgetting tenant/user identity in cache keys.
- Caching sensitive user-specific data under a shared key.
- Treating cache as the source of truth.
- Increasing replicas without considering database connection limits.

## 10. Interview questions

**Q1. Vertical vs horizontal scaling?** Add resources to one machine vs add more machines/instances.

**Q2. Cache-aside?** Read cache first; on miss read source and populate cache.

**Q3. What is cache stampede?** Many requests reload the same expired/missing data simultaneously.

**Q4. Why can local cache be a problem?** Each app instance has its own copy and can serve different versions.

**Q5. What is a load balancer health check?** A probe used to decide whether an instance should receive traffic.

## 11. Practice

Design caching for tenant-specific master data in a three-instance Spring Boot deployment. Define key format, TTL, invalidation, cold-start behavior and failure handling.
