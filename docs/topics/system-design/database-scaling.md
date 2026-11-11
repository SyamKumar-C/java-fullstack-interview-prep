# Database Scaling and Data Access Design

## 1. Start with measurement

Find the bottleneck before changing architecture. Inspect query plans, slow-query logs, CPU, I/O, lock waits, connection pools, cache hit rate and request patterns.

## 2. Indexing

An index can speed up reads by reducing rows scanned, but costs storage and adds work to inserts, updates and deletes. Index columns based on real predicates, joins and ordering. Composite index column order matters; validate with EXPLAIN/EXPLAIN ANALYZE.

## 3. Connection pooling

A pool reuses database connections and limits concurrent database work. Too many connections can overload the database. Size the pool using database capacity, number of application instances and workload, not just CPU count.

## 4. Read replicas

Read replicas can offload read traffic. Replication is often asynchronous, so a recent write may not immediately appear on a replica. Route consistency-sensitive reads to the primary or use an explicit read-your-writes strategy.

## 5. Caching

Cache frequently read data with a clear TTL and invalidation plan. Cache-aside is common: read cache, load database on miss, then populate cache. Define behavior for stale data, concurrent misses and cache outages.

## 6. Partitioning and sharding

- Partitioning splits a table inside a database, often by date or key range.
- Sharding distributes data across multiple database nodes.

Partitioning can help manage large tables and prune scans. Sharding adds routing, cross-shard query, rebalancing and transaction complexity. Do not shard before simpler options are insufficient.

## 7. Denormalization and materialized views

Denormalization stores derived data to make reads faster but adds consistency/update work. Materialized views store query results and need refresh/update strategies.

## 8. Pagination

Offset pagination is simple but large offsets can be expensive and concurrent inserts can shift pages. Keyset pagination uses the last seen sort key and is often better for large, ordered datasets.

```sql
SELECT id, created_at
FROM orders
WHERE (created_at, id) < (:lastCreatedAt, :lastId)
ORDER BY created_at DESC, id DESC
LIMIT 50;
```

Use a stable unique tie-breaker.

## 9. Practical scenario

A delivery-jobs API returns millions of rows. Add filters and a composite index matching the query, select only required columns, use keyset pagination, cache stable reference data and measure query plans. Do not return the entire table and rely on frontend filtering.

## 10. Common mistakes

- Adding indexes without checking query plans.
- Creating too many indexes.
- Increasing connection pool size without database capacity analysis.
- Reading from replicas when immediate consistency is required.
- Sharding prematurely.
- Using offset pagination for deep pages at very large scale.

## 11. Interview questions

**Q1. How do you optimize a slow query?** Inspect execution plan, predicates, joins, indexes, row counts and returned columns; measure after changes.

**Q2. Why use a read replica?** Offload read workload, accepting replication lag trade-offs.

**Q3. Partitioning vs sharding?** Partitioning divides tables within a database; sharding distributes data across database nodes.

**Q4. What is keyset pagination?** Continue from the last seen sort key rather than skipping a large offset.

**Q5. Why not create indexes on every column?** Indexes consume storage and slow writes/maintenance.

## 12. Practice

Take a slow paginated query, design a composite index, compare EXPLAIN ANALYZE before/after, and propose a keyset pagination version.
