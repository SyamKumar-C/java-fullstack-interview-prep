# SQL and Databases

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 3–4 hours

SQL is used to define, query, and modify relational data. Interview preparation should include both query writing and understanding how the database executes a query.

## 1. Joins and subqueries

- INNER JOIN returns matching rows from both sides.
- LEFT JOIN returns all left rows and matching right rows, using NULL for missing matches.
- RIGHT JOIN and FULL OUTER JOIN provide the corresponding right/full behavior where supported.
- A subquery is a query nested inside another query.
- A correlated subquery refers to values from the outer query and may be evaluated repeatedly or optimized by the database.

## 2. Common table expressions (CTEs)

A CTE names a query result for use by the statement that follows it.

```sql
WITH department_totals AS (
    SELECT department_id, SUM(salary) AS total_salary
    FROM employees
    GROUP BY department_id
)
SELECT department_id, total_salary
FROM department_totals
WHERE total_salary > 100000;
```

Recursive CTEs can process hierarchical data such as reporting structures or category trees. CTE materialization and optimization depend on the database engine and query.

## 3. Window functions

Window functions calculate values across related rows without collapsing them into one row per group.

```sql
SELECT employee_id, department_id, salary,
       DENSE_RANK() OVER (
           PARTITION BY department_id
           ORDER BY salary DESC
       ) AS salary_rank
FROM employees;
```

Know ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD, and aggregate window functions.

## 4. Indexes

Indexes help the database find rows without scanning the entire table in many cases. They add storage and write overhead.

Common types and ideas:
- B-tree indexes for equality and range searches.
- Composite indexes for multiple columns; column order matters.
- Covering indexes can satisfy a query using index data alone.
- Partial/filtered indexes include only rows matching a condition (support varies).
- Expression/function-based indexes index a calculated expression.

Creating an index does **not** guarantee the query planner will use it. The planner compares estimated costs, selectivity, table size, statistics, and query shape. Use EXPLAIN (and where appropriate EXPLAIN ANALYZE) to inspect the plan.

## 5. ACID and isolation

- Atomicity: all or nothing.
- Consistency: constraints and invariants remain valid.
- Isolation: concurrent transactions are controlled.
- Durability: committed changes survive failures according to database guarantees.

Understand dirty reads, non-repeatable reads, phantom reads, and the isolation levels supported by your database.

## 6. Normalization and constraints

Normalization reduces duplication and update anomalies. Primary keys identify rows; foreign keys enforce relationships; unique, check, and not-null constraints protect data integrity.

## 7. Query performance

1. Capture the slow query and its parameters.
2. Inspect EXPLAIN / EXPLAIN ANALYZE.
3. Check row estimates, actual rows, scans, joins, sorting, and loops.
4. Verify statistics and index suitability.
5. Reduce unnecessary columns and rows.
6. Re-test with realistic data and concurrency.

## 8. Interview questions

??? question "Does creating an index mean the database always uses it?"
    No. The optimizer chooses a plan based on estimated cost. For a small table or a query returning a large fraction of rows, a sequential scan may be cheaper.

??? question "What is the difference between WHERE and HAVING?"
    WHERE filters rows before grouping. HAVING filters groups after GROUP BY and can use aggregate results.

??? question "What is the difference between ROW_NUMBER, RANK, and DENSE_RANK?"
    ROW_NUMBER assigns a unique sequential number. RANK gives ties the same rank and leaves gaps. DENSE_RANK gives ties the same rank without gaps.

??? question "What is a composite index?"
    It is an index on multiple columns. The order of columns affects which filters and sort patterns can use it efficiently.

??? question "What is a CTE?"
    A common table expression is a named query result defined with WITH and available to the main statement. It improves query structure and can also support recursive queries.

## 9. Practice

1. Find the second-highest distinct salary.
2. Find the top three salaries per department.
3. Find customers who have never placed an order.
4. Use a CTE to calculate department totals.
5. Compare EXPLAIN plans with and without an index.
