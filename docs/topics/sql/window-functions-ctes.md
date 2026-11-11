# CTEs and Window Functions

A CTE names a query result for one statement and can improve readability or support recursion.

```sql
WITH totals AS (
  SELECT department_id, SUM(salary) total
  FROM employees GROUP BY department_id
)
SELECT * FROM totals WHERE total > 100000;
```

CTEs are not automatically faster than subqueries; optimizer behavior varies.

## Window functions
They calculate across related rows without collapsing them into one row per group.

```sql
SELECT employee_id, department_id, salary,
 DENSE_RANK() OVER (
   PARTITION BY department_id ORDER BY salary DESC
 ) AS salary_rank
FROM employees;
```

Know ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD, SUM OVER and AVG OVER.

GROUP BY reduces rows; window functions retain rows and add calculated values. RANK leaves gaps after ties; DENSE_RANK does not.
