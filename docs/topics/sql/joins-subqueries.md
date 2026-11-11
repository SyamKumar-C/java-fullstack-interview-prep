# SQL Joins and Subqueries

## Joins
INNER returns matching rows. LEFT returns all left rows plus matches. RIGHT returns all right rows plus matches. FULL OUTER returns all rows from both sides (support varies). CROSS returns the Cartesian product.

```sql
SELECT o.id, c.name
FROM orders o JOIN customers c ON c.id = o.customer_id;
```

## ON vs WHERE with outer joins
ON controls matching. WHERE filters the joined result and can remove NULL-extended rows, effectively making a LEFT JOIN behave like an INNER JOIN for that condition.

## Subqueries
A subquery can return scalar values, rows or correlated results. EXISTS is useful for checking whether a matching row exists.

```sql
SELECT c.id FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
```

**Interview:** JOIN vs EXISTS? JOIN combines rows; EXISTS tests whether at least one match exists.
