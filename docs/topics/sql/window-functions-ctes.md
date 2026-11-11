# SQL CTEs and Window Functions

## 1. Common Table Expression (CTE)

A CTE is a named query result scoped to one SQL statement. It can make complex queries easier to read and can support recursive queries.

```sql
WITH department_totals AS (
    SELECT department_id, SUM(salary) AS total_salary
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM department_totals
WHERE total_salary > 100000;
```

A CTE is not automatically faster than a subquery. The optimizer decides the execution plan; behavior varies by database and query.

## 2. Multiple CTEs

```sql
WITH active_employees AS (
    SELECT * FROM employees WHERE status = 'ACTIVE'
),
department_counts AS (
    SELECT department_id, COUNT(*) AS employee_count
    FROM active_employees
    GROUP BY department_id
)
SELECT * FROM department_counts;
```

Use meaningful names and keep each CTE focused.

## 3. Recursive CTE

Recursive CTEs can traverse hierarchies such as employee-manager relationships or category trees. They have an anchor query and a recursive member. Use cycle/depth protection where the database supports it and the data can contain cycles.

## 4. Window functions

A window function calculates across related rows while retaining each row. Unlike GROUP BY, it does not collapse rows into one row per group.

```sql
SELECT employee_id, department_id, salary,
       DENSE_RANK() OVER (
         PARTITION BY department_id
         ORDER BY salary DESC
       ) AS salary_rank
FROM employees;
```

- PARTITION BY defines groups.
- ORDER BY defines order within each group.
- The frame clause defines which rows contribute for frame-sensitive functions.

## 5. Ranking functions

| Function | Ties | Example ranks for values 100, 100, 90 |
|---|---|---|
| ROW_NUMBER | Unique sequence | 1, 2, 3 |
| RANK | Same rank, leaves gaps | 1, 1, 3 |
| DENSE_RANK | Same rank, no gaps | 1, 1, 2 |

Add a deterministic tie-breaker to ORDER BY when stable row numbering matters.

## 6. Other useful window functions

- LAG: read a previous row's value.
- LEAD: read a following row's value.
- SUM/AVG/COUNT OVER: running or partition aggregates.
- FIRST_VALUE/LAST_VALUE: first/last value within the defined window frame.

```sql
SELECT order_date, amount,
       SUM(amount) OVER (
         ORDER BY order_date
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_total
FROM orders;
```

## 7. GROUP BY vs window function

GROUP BY returns one row per group. A window function keeps the original rows and adds a calculated value to each row.

## 8. Practical scenarios

- Second-highest salary per department: DENSE_RANK partitioned by department.
- Running account balance: SUM OVER ordered by transaction time.
- Compare current sale to previous sale: LAG.
- Find latest order per customer: ROW_NUMBER partitioned by customer and ordered descending.

## 9. Common mistakes

- Forgetting PARTITION BY and ranking across the whole table.
- Using RANK when gaps are not wanted.
- Assuming window ORDER BY sorts the final result; add an outer ORDER BY.
- Ignoring window frame behavior for running totals and LAST_VALUE.
- Assuming CTEs always materialize or always improve speed.

## 10. Interview questions

**Q1. CTE vs subquery?** A CTE names a query result for readability/reuse in a statement; performance depends on optimizer behavior.

**Q2. GROUP BY vs window?** GROUP BY collapses rows; window functions preserve rows.

**Q3. RANK vs DENSE_RANK?** RANK leaves gaps after ties; DENSE_RANK does not.

**Q4. What does PARTITION BY do?** It divides rows into independent groups for the window calculation.

**Q5. How do you get the latest row per customer?** Use ROW_NUMBER() over partition by customer ordered by date descending, then filter row number 1.

## 11. Practice with solutions

Second-highest distinct salary per department:

```sql
WITH ranked AS (
  SELECT department_id, employee_id, salary,
         DENSE_RANK() OVER (
           PARTITION BY department_id ORDER BY salary DESC
         ) AS rnk
  FROM employees
)
SELECT department_id, employee_id, salary
FROM ranked
WHERE rnk = 2;
```

Running total:

```sql
SELECT account_id, txn_time, amount,
       SUM(amount) OVER (
         PARTITION BY account_id ORDER BY txn_time, id
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_balance
FROM transactions;
```
