# SQL Indexes and Execution Plans

## 1. What is an index?

An index is a data structure the database maintains to find rows without scanning the entire table. It can speed up reads, joins, filtering, and ordering, but it consumes storage and adds work to INSERT, UPDATE, and DELETE.

An index is not a command that forces the database to use it. The query optimizer chooses a plan based on statistics, estimated costs, table size, selectivity, and available access paths.

## 2. Common index types

Exact options vary by database engine.

- B-tree: common general-purpose index for equality, range conditions, and ordering.
- Hash: equality lookups in engines that support it; not suitable for range ordering.
- Composite index: index over multiple columns.
- Unique index: enforces uniqueness and supports lookup.
- Partial/filtered index: indexes only rows matching a predicate (database-specific).
- Expression/function-based index: indexes the result of an expression, such as lower(email).
- Full-text index: supports text search.
- Specialized indexes: for example PostgreSQL GIN/GiST/BRIN for particular data types and workloads.

## 3. Creating an index

```sql
CREATE INDEX idx_orders_customer_id
ON orders(customer_id);

CREATE UNIQUE INDEX ux_users_email
ON users(email);

-- PostgreSQL example: index a normalized expression
CREATE INDEX idx_users_lower_email
ON users (lower(email));
```

A query using `lower(email) = lower(?)` may be able to use the expression index if the expression matches the indexed expression and the optimizer considers it beneficial.

## 4. Composite index and column order

```sql
CREATE INDEX idx_orders_status_created
ON orders(status, created_at);
```

This index is generally useful for queries filtering by status, and often for status plus a created_at range. It may be less useful for a query filtering only by created_at because the leading column is missing. This is commonly called the leftmost-prefix principle for B-tree composite indexes; details vary by database.

Choose column order based on real query patterns, selectivity, sort requirements, and engine behavior—not a universal rule that the most selective column must always be first.

## 5. Selectivity and index usage

Selectivity describes how well a predicate narrows rows. A highly selective condition may make an index attractive. If a query returns a large fraction of the table, a sequential/table scan can be cheaper than many random index lookups.

Other reasons an index may not be used:
- Table is small.
- Statistics are stale or inaccurate.
- Query applies a function or cast that prevents matching a normal index.
- Data type mismatch or implicit conversion.
- Leading wildcard pattern such as LIKE '%term' (ordinary B-tree usually cannot help).
- Composite index leading column is not constrained.
- The index adds more cost than it saves.

## 6. Read an execution plan

PostgreSQL:
```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM orders
WHERE customer_id = 42;
```

- EXPLAIN shows the estimated plan.
- ANALYZE executes the query and reports actual timings and row counts.
- BUFFERS reports buffer activity.

Compare estimated rows with actual rows. Large differences may indicate poor statistics or estimation problems. Inspect scan type, join type, sort, filters, loops, and buffer reads.

Be careful: EXPLAIN ANALYZE executes the statement. For UPDATE/DELETE, use a safe transaction and rollback if appropriate.

## 7. Index-only scans and covering indexes

Some databases can answer a query from index data without visiting the table for every row, when the index contains required columns and visibility/engine conditions permit it. A covering index is designed to include the columns needed by a query. PostgreSQL supports INCLUDE columns for this purpose.

Do not add every selected column to every index. Wider indexes use more storage and increase write costs.

## 8. When not to add an index

- Tiny tables where a scan is cheaper.
- Columns rarely filtered or joined.
- Very low-selectivity columns, unless a composite or partial index fits a query.
- Tables with heavy writes and little read benefit.
- Duplicate or overlapping indexes that add maintenance cost.

## 9. Practical troubleshooting process

1. Identify the slow query and realistic parameter values.
2. Capture its execution plan.
3. Check estimated versus actual rows.
4. Check filters, joins, sorts, and repeated loops.
5. Review existing indexes and data distribution.
6. Add or adjust an index only when the plan supports the change.
7. Re-run with representative data and compare latency and write impact.

## 10. Interview questions and answers

**Q1. Does creating an index guarantee that the database uses it?**  
No. The optimizer chooses the cheapest estimated plan. A table scan can be faster for small tables or queries returning many rows.

**Q2. Why does column order matter in a composite index?**  
B-tree navigation is organized by the leading key columns. Queries that constrain the leading columns can use the index more effectively.

**Q3. What is an expression index?**  
An index on a computed expression, such as lower(email). It helps when queries use a matching expression.

**Q4. Why can too many indexes hurt?**  
Each write may need to update multiple indexes. They consume disk and memory and increase maintenance work.

**Q5. What is selectivity?**  
How strongly a predicate narrows the result set. More selective predicates often make index access more attractive.

**Q6. Difference between EXPLAIN and EXPLAIN ANALYZE in PostgreSQL?**  
EXPLAIN shows the estimated plan without running the query. EXPLAIN ANALYZE runs it and reports actual execution information.

**Q7. What is a covering index?**  
An index containing all columns needed by a query, potentially allowing the database to answer from the index without fetching table rows.

## 11. Practice exercises

1. Given a query filtering by customer_id and sorting by created_at, propose an index and explain the column order.
2. Explain why an index on email may not help a query using lower(email).
3. Compare EXPLAIN and EXPLAIN ANALYZE for a query returning 90% of a table.
4. Identify write-heavy tables where a new index may do more harm than good.

## 12. Quick revision

Indexes trade write/storage cost for read speed. The optimizer chooses whether to use them. Composite order and query shape matter. Always inspect the execution plan before adding indexes.
