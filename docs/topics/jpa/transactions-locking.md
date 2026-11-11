# JPA Transactions, Flush and Locking

## 1. Persistence context and transaction

JPA EntityManager manages a persistence context containing managed entities. Changes to managed entities are tracked and synchronized with the database during flush.

A transaction defines the unit of work and commit/rollback boundary. In Spring applications, @Transactional commonly provides this boundary.

## 2. Flush vs commit

Flush synchronizes pending persistence-context changes to SQL statements sent to the database. Commit completes the transaction.

Flush can happen before commit, for example:
- Explicitly via `entityManager.flush()`.
- Before a query under AUTO flush mode when pending changes may affect query results.
- During transaction completion.

A flush is not a commit. A later rollback can still undo flushed SQL.

## 3. Dirty checking

```java
@Transactional
public void rename(long id) {
    User user = entityManager.find(User.class, id);
    user.setName("Updated");
    // Hibernate detects the change and issues UPDATE at flush.
}
```

The entity must be managed. Changes to detached entities are not automatically tracked.

## 4. Optimistic locking

Use `@Version` to detect concurrent updates.

```java
@Entity
class Product {
    @Id Long id;
    int stock;
    @Version
    long version;
}
```

The provider includes the version in the update condition and increments it. If another transaction has changed the row, the update affects no row and an optimistic locking exception is raised. The application can retry or return a conflict response depending on business rules.

Best when conflicts are uncommon and you do not want to hold database locks for the whole read/edit period.

## 5. Pessimistic locking

Pessimistic locking asks the database to lock selected rows.

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("select p from Product p where p.id = :id")
Optional<Product> findForUpdate(long id);
```

It can block other transactions and can cause lock waits/deadlocks. Keep the transaction short and configure appropriate timeouts.

## 6. Optimistic vs pessimistic

| Optimistic | Pessimistic |
|---|---|
| Detects conflict at update/commit | Acquires database lock |
| Good when conflicts are rare | Useful for high-contention critical updates |
| May require retry | Can block other transactions |
| Uses version/compare check | Uses DB locking |

## 7. Practical scenarios

- Product description editing: optimistic locking is often enough.
- Last-seat/last-item reservation: conditional SQL update or pessimistic lock may be appropriate.
- Long user editing session: do not hold a database transaction open; use version checking on save.

## 8. Common mistakes

- Assuming flush equals commit.
- Assuming save() always sends SQL immediately.
- Using pessimistic locks around remote service calls.
- Ignoring optimistic locking exceptions.
- Assuming @Version prevents every business race if updates bypass JPA.
- Holding locks for long periods.

## 9. Interview questions

**Q1. What is flush?** Synchronizes pending persistence-context changes with the database.

**Q2. Flush vs commit?** Flush sends SQL; commit completes the transaction.

**Q3. What is dirty checking?** Hibernate detects changes to managed entities and writes updates during flush.

**Q4. Optimistic vs pessimistic locking?** Optimistic detects conflicts using version checks; pessimistic obtains database locks.

**Q5. What does @Version do?** It detects concurrent modifications by checking and updating a version column.

## 10. Practice

Run two transactions that load and update the same Product. First add @Version and observe the conflict. Then use PESSIMISTIC_WRITE and compare blocking behavior.
