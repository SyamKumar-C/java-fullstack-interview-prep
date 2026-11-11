# SQL Joins and Subqueries

## 1. Why joins?

Relational data is split across tables to reduce duplication and represent relationships. A join combines related rows using a condition.

Example tables:
- customers(customer_id, name)
- orders(order_id, customer_id, total)

## 2. INNER JOIN

Returns only rows with matching records on both sides.

```sql
SELECT c.name, o.order_id
FROM customers c
INNER JOIN orders o ON o.customer_id = c.customer_id;
```

Customers without orders are excluded.

## 3. LEFT JOIN

Returns every row from the left table and matching rows from the right. When there is no match, right-side columns are NULL.

```sql
SELECT c.name, o.order_id
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id;
```

To find customers with no orders:
```sql
SELECT c.*
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;
```

## 4. RIGHT and FULL OUTER JOIN

RIGHT JOIN preserves all rows from the right table. FULL OUTER JOIN preserves unmatched rows from both sides. Support and syntax vary by database; PostgreSQL supports FULL OUTER JOIN, while some engines require a UNION-based approach.

## 5. CROSS JOIN and self join

CROSS JOIN returns the Cartesian product: each left row paired with every right row. Use it deliberately.

A self join joins a table to itself, for example employee to manager using manager_id.

## 6. ON versus WHERE with outer joins

This is a common interview trap.

```sql
-- Keeps all customers; only matching paid orders are joined
SELECT c.name, o.order_id
FROM customers c
LEFT JOIN orders o
  ON o.customer_id = c.customer_id
 AND o.status = 'PAID';
```

If `o.status = 'PAID'` is moved to WHERE, rows with no order have NULL status and are filtered out, effectively changing the result toward an inner join for that condition.

## 7. Subqueries

A subquery is a query nested inside another query. It can appear in SELECT, FROM, or WHERE depending on the database.

Scalar subquery:
```sql
SELECT name
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

IN subquery:
```sql
SELECT *
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);
```

EXISTS:
```sql
SELECT c.*
FROM customers c
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

EXISTS checks whether at least one matching row exists. The optimizer may transform IN and EXISTS into similar plans; do not assume one is always faster.

## 8. Correlated subquery

A correlated subquery refers to a value from the outer query and may logically be evaluated for each outer row, though the optimizer may transform it.

```sql
SELECT e.*
FROM employees e
WHERE e.salary > (
  SELECT AVG(e2.salary)
  FROM employees e2
  WHERE e2.department_id = e.department_id
);
```

This returns employees paid above their department average.

## 9. JOIN versus subquery

Both can express many of the same problems. Choose the clearest form, then inspect the execution plan for performance-sensitive queries. EXISTS is often clear for existence checks; joins are natural when columns from both tables are needed.

## 10. Common mistakes

- Joining on the wrong key and multiplying rows.
- Forgetting that one-to-many joins return multiple rows per parent.
- Moving right-table filters from ON to WHERE in a LEFT JOIN unintentionally.
- Using SELECT * in production queries without need.
- Assuming IN is always slower than EXISTS.
- Ignoring NULL behavior, especially with NOT IN when the subquery can return NULL.

## 11. Interview questions and answers

**Q1. INNER JOIN versus LEFT JOIN?**  
INNER returns matches only. LEFT preserves every left-side row and fills right-side columns with NULL when there is no match.

**Q2. WHERE versus ON for a LEFT JOIN?**  
ON decides which right rows match; WHERE filters the final joined result. A right-side WHERE predicate can remove NULL-extended rows.

**Q3. JOIN versus EXISTS?**  
Use JOIN when you need columns from both tables. Use EXISTS when the question is whether a related row exists. Optimizers can transform either form.

**Q4. What is a correlated subquery?**  
A nested query that refers to a column from the outer query.

**Q5. What is a Cartesian product?**  
Every row from one input paired with every row from the other; CROSS JOIN produces it explicitly.

**Q6. Why is NOT IN dangerous with NULL?**  
If the subquery returns NULL, SQL's three-valued logic can make comparisons UNKNOWN and filter rows unexpectedly. NOT EXISTS is often safer.

## 12. Practice

1. List customers who have never placed an order.
2. List each customer and total order count.
3. Find employees earning above their department average.
4. Show all customers with only their paid orders, preserving customers with no paid order.
5. Explain why joining orders to order_items changes row counts.

## 13. Quick revision

INNER = matches only. LEFT = preserve left rows. ON defines matching; WHERE filters final rows. EXISTS tests presence. Watch NULL and one-to-many row multiplication.
