# SQL Grouping, Constraints, NULL Semantics and Views

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Short interview answer
Constraints protect data integrity. GROUP BY forms groups for aggregate calculations, and HAVING filters those groups. NULL represents an unknown or missing value and follows three-valued logic. A normal view stores a query definition; a materialized view stores query results that need refreshing.

## 2. Constraints
- PRIMARY KEY: uniquely identifies a row and disallows NULL.
- FOREIGN KEY: enforces a reference to a key in another table.
- UNIQUE: prevents duplicate key values; NULL behavior varies by database.
- NOT NULL: requires a value.
- CHECK: enforces a row condition.
- DEFAULT: supplies a value when an insert omits the column.

```sql
CREATE TABLE orders (
  id BIGINT PRIMARY KEY,
  customer_id BIGINT NOT NULL,
  total NUMERIC(12,2) CHECK (total >= 0),
  status VARCHAR(20) NOT NULL,
  FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

Constraints are stronger than application-only validation because all writers must obey them.

## 3. GROUP BY and HAVING

```sql
SELECT customer_id, COUNT(*) AS order_count, SUM(total) AS revenue
FROM orders
WHERE status = 'PAID'
GROUP BY customer_id
HAVING COUNT(*) >= 3
ORDER BY revenue DESC;
```

Conceptual order: FROM/JOIN, WHERE, GROUP BY, HAVING, SELECT, ORDER BY. WHERE filters individual rows; HAVING filters groups after aggregation.

## 4. NULL semantics
A comparison such as `column = NULL` is not true. Use `IS NULL` or `IS NOT NULL`. SQL conditions can be TRUE, FALSE, or UNKNOWN; WHERE keeps only TRUE rows.

- `NULL = NULL` is UNKNOWN.
- `COUNT(*)` counts rows.
- `COUNT(column)` ignores NULL in that column.
- `SUM(column)` ignores NULL values and may return NULL if there are no non-NULL inputs.
- `COALESCE(value, fallback)` returns the first non-NULL expression.
- `NOT IN` can behave unexpectedly if its subquery contains NULL; consider NOT EXISTS.

## 5. Views

```sql
CREATE VIEW paid_order_summary AS
SELECT customer_id, COUNT(*) AS order_count
FROM orders
WHERE status = 'PAID'
GROUP BY customer_id;
```

A normal view stores the query definition. A materialized view stores query results and must be refreshed. PostgreSQL example:

```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT date_trunc('month', created_at) AS month, SUM(total) AS revenue
FROM orders
WHERE status = 'PAID'
GROUP BY 1;

REFRESH MATERIALIZED VIEW monthly_sales;
```

Materialized views can speed up expensive dashboard queries, but data may be stale between refreshes and storage is required. Refresh options differ by database.

## 6. Common mistakes
- Putting aggregate filters in WHERE instead of HAVING.
- Using = NULL.
- Assuming COUNT(column) counts NULL values.
- Using NOT IN without considering NULL in its subquery.
- Treating a normal view as cached data.
- Forgetting to refresh a materialized view.

## 7. Interview questions
**Q1. WHERE vs HAVING?** WHERE filters rows before grouping; HAVING filters groups after aggregation.

**Q2. COUNT(*) vs COUNT(column)?** The first counts rows; the second counts non-NULL values in that column.

**Q3. Why is = NULL wrong?** NULL comparisons evaluate to UNKNOWN; use IS NULL.

**Q4. View vs materialized view?** A view stores a query definition; a materialized view stores results and needs refresh.

**Q5. Why use constraints?** They protect integrity for every application instance and every data writer.

## 8. Practice
1. Find customers with more than five paid orders.
2. Find customers with no orders using NOT EXISTS.
3. Compare COUNT(*) and COUNT(phone) with NULL phone values.
4. Design a materialized view for a dashboard and explain its freshness limit.
