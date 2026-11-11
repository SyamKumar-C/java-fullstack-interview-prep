# SQL Transactions and Database Normalization

## 1. Transactions and ACID

A transaction groups statements into one logical unit.

- Atomicity: all changes commit or roll back.
- Consistency: constraints and business invariants remain valid.
- Isolation: concurrent work is controlled according to the isolation level.
- Durability: committed data persists according to database guarantees.

```sql
BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;
```

If a required step fails, issue ROLLBACK. Application code should also verify that the expected rows were updated.

## 2. Concurrency anomalies

- Dirty read: reading another transaction's uncommitted change.
- Non-repeatable read: reading the same row twice and seeing a committed change between reads.
- Phantom read: repeating a predicate query and seeing rows added/removed by another transaction.
- Lost update: concurrent updates overwrite each other's result when not properly coordinated.

The exact anomaly prevention depends on database engine and isolation implementation.

## 3. Isolation levels

Common SQL levels are READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, and SERIALIZABLE. Databases implement them differently. PostgreSQL's REPEATABLE READ uses snapshot isolation and prevents some anomalies beyond the minimum SQL definition, but serialization anomalies can still be relevant at lower levels. SERIALIZABLE may abort a transaction that must be retried.

## 4. Locks and optimistic concurrency

Pessimistic locking holds locks while work is performed, for example SELECT ... FOR UPDATE in supported databases.

Optimistic locking detects conflicting updates using a version column:
```sql
UPDATE product
SET stock = stock - 1, version = version + 1
WHERE id = 10 AND version = 4;
```

If zero rows are updated, another transaction may have changed the record. The application can reload and retry or report a conflict.

## 5. Normalization

Normalization organizes relational tables to reduce duplicate data and update anomalies.

- 1NF: each column holds atomic values under the chosen relational model; no repeating groups.
- 2NF: in 1NF, and every non-key attribute depends on the whole candidate key (no partial dependency on part of a composite key).
- 3NF: in 2NF, and non-key attributes do not depend transitively on a candidate key through another non-key attribute (formal definitions use functional dependencies).
- BCNF: every determinant of a non-trivial functional dependency is a superkey.

## 6. Example of an update anomaly

A table stores orderId, customerId, customerName, and orderTotal. If the customer name is repeated across many orders, changing the name requires updating many rows. A separate Customer table avoids this duplication; Orders stores customerId as a foreign key.

## 7. Denormalization

Denormalization intentionally duplicates or precomputes data to improve read performance or simplify reporting. It adds consistency and update complexity. Use it based on measured workload and a clear mechanism to keep data synchronized.

## 8. Common mistakes

- Treating ACID as a performance guarantee.
- Assuming every database implements isolation levels identically.
- Retrying a transaction without making the operation safe to repeat.
- Using pessimistic locks for every update.
- Normalizing blindly without considering read patterns.
- Denormalizing without a consistency strategy.

## 9. Interview questions and answers

**Q1. Explain ACID.**  
Atomicity all-or-nothing; Consistency preserves rules; Isolation controls concurrent visibility; Durability preserves committed data.

**Q2. Dirty read versus non-repeatable read?**  
Dirty read sees uncommitted data. Non-repeatable read sees different committed values for the same row across reads in one transaction.

**Q3. What is 2NF?**  
A relation is in 1NF and every non-key attribute depends on the whole candidate key, not just part of a composite key.

**Q4. What is 3NF?**  
It removes relevant transitive dependencies of non-key attributes on candidate keys.

**Q5. Normalization versus denormalization?**  
Normalization reduces redundancy and update anomalies. Denormalization adds controlled redundancy to improve read patterns at the cost of consistency work.

**Q6. Optimistic versus pessimistic locking?**  
Optimistic locking checks for conflicts at update time, often with a version. Pessimistic locking acquires locks to prevent conflicting changes while work is in progress.

## 10. Practice

Design customer, order, and order_item tables. Identify primary and foreign keys, explain why customer details should not be repeated in every order row, and choose an isolation/locking approach for stock decrement.

## 11. Quick revision

Transactions protect multi-statement work. Isolation levels control concurrent visibility. Normalization reduces redundancy; denormalization is a measured read optimization.
