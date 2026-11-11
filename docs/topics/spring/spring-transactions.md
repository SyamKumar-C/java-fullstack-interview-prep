# Spring Transactions

## 1. What is a transaction?

A transaction groups database operations into one logical unit. ACID means:
- Atomicity: all operations commit or none do.
- Consistency: database constraints/business invariants remain valid.
- Isolation: concurrent transactions are separated according to the selected level.
- Durability: committed data survives failures according to database guarantees.

Spring's `@Transactional` declares a transaction boundary. A transaction manager coordinates the underlying resource, commonly a JDBC connection or JPA EntityManager.

## 2. Basic @Transactional example

```java
@Service
@RequiredArgsConstructor
public class TransferService {
    private final AccountRepository accounts;

    @Transactional
    public void transfer(long fromId, long toId, BigDecimal amount) {
        Account from = accounts.findById(fromId).orElseThrow();
        Account to = accounts.findById(toId).orElseThrow();

        from.debit(amount);
        to.credit(amount);
        // Managed entities are flushed/committed when the method completes.
    }
}
```

If the transaction rolls back, both database updates are undone. This assumes both operations use the same transaction manager/resource and participate in the transaction.

## 3. How @Transactional works

In Spring's common proxy mode, a proxy wraps calls to transactional methods:
1. Caller invokes the Spring-managed bean through its proxy.
2. Transaction interceptor checks propagation and starts or joins a transaction.
3. Target method executes.
4. On normal completion, transaction commits.
5. On a rollback-triggering exception, transaction rolls back.

Self-invocation does not pass through the proxy:

```java
@Service
class ReportService {
    public void generate() {
        saveReport(); // this.saveReport(): proxy is bypassed
    }

    @Transactional
    public void saveReport() {
        // Annotation may not be applied for this self-call.
    }
}
```

Move the transactional method to another Spring bean or call through an appropriate proxy boundary. AspectJ mode has different behavior.

## 4. Rollback rules

### Default behavior

By default, Spring rolls back for:
- `RuntimeException` and its subclasses.
- `Error` and its subclasses.

By default, Spring does not roll back for checked exceptions such as IOException. The transaction normally commits if a checked exception escapes and no other rollback rule applies.

### rollbackFor

Use `rollbackFor` when a checked exception should cause rollback.

```java
@Transactional(rollbackFor = IOException.class)
public void importFile() throws IOException {
    repository.save(batch);
    fileParser.parse(); // IOException causes rollback
}
```

You can specify multiple classes:

```java
@Transactional(rollbackFor = {
    IOException.class,
    SQLException.class
})
public void process() throws IOException, SQLException {
    // database work
}
```

### noRollbackFor

Use `noRollbackFor` when a matching exception should not cause rollback under the configured rules.

```java
@Transactional(noRollbackFor = BusinessWarningException.class)
public void processOrder() {
    repository.save(order);
    validateWarningOnly();
}
```

Use this carefully: committing after an exception is a business decision, not a generic fix.

### Catching exceptions

If an exception is caught and not rethrown, the transaction interceptor may see normal method completion and commit.

```java
@Transactional
public void update() {
    repository.save(first);
    try {
        riskyOperation();
    } catch (RuntimeException ex) {
        log.warn("Continuing", ex);
        // If not marked rollback-only, transaction may commit.
    }
}
```

If the work must roll back, rethrow the exception or mark the transaction rollback-only using the appropriate transaction API. A participating inner transaction may also mark the shared transaction rollback-only; the outer caller can then receive UnexpectedRollbackException when it attempts to commit.

### Rollback rule priority

When multiple rules match, the most specific matching rule is applied. Prefer class-based rollback rules rather than broad name patterns when possible.

### Important distinction

A rollback only undoes work managed by that transaction. It cannot undo an email already sent, an HTTP request already processed by another service, or a message published outside a coordinated transaction. Use an outbox/Saga or compensating action for distributed work.

## 5. Propagation

Propagation defines what a transactional method does when a transaction already exists.

### REQUIRED (default)

Join the current transaction; if none exists, create one.

```java
@Transactional
public void placeOrder() {
    orderRepository.save(order);
    inventoryService.reserve(); // REQUIRED joins this transaction if same manager
}
```

If the inner participating method fails with a rollback-triggering exception, the shared transaction may be marked rollback-only.

### REQUIRES_NEW

Suspend the current transaction and start an independent transaction. The inner transaction can commit even if the outer transaction later rolls back.

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void saveAuditRecord(AuditRecord record) {
    auditRepository.save(record);
}
```

Use case: save an audit/failure record independently of the main business transaction. Be aware that the suspended outer transaction may still hold a database connection while the inner transaction needs another one. Under load, pool sizing and nested calls can cause connection starvation.

### SUPPORTS

Join a transaction if one exists; otherwise execute without a transaction.

### MANDATORY

Require an existing transaction. If there is none, Spring throws an exception. Useful for internal methods that must only run inside a larger unit of work.

### NOT_SUPPORTED

Suspend an existing transaction and execute without one. Useful for work that should not hold a database transaction open.

### NEVER

Execute without a transaction and fail if one already exists.

### NESTED

Run within a nested transaction when supported, commonly using a JDBC savepoint. It is not the same as REQUIRES_NEW: a nested rollback can roll back to a savepoint while the outer transaction continues. Support depends on the transaction manager and resource.

### Propagation comparison

| Type | Existing transaction? | No existing transaction? |
|---|---|---|
| REQUIRED | Join | Create |
| REQUIRES_NEW | Suspend and create independent | Create |
| SUPPORTS | Join | No transaction |
| MANDATORY | Join | Throw exception |
| NOT_SUPPORTED | Suspend | No transaction |
| NEVER | Throw exception | No transaction |
| NESTED | Nested/savepoint if supported | Create transaction |

## 6. Isolation levels

Isolation controls what one transaction can observe while other transactions are changing data.

### Read phenomena

- Dirty read: read another transaction's uncommitted change.
- Non-repeatable read: read the same row twice and get different committed values because another transaction updated it.
- Phantom read: repeat a predicate query and see a different set of rows because another transaction inserted/deleted matching rows.

### READ_UNCOMMITTED

Lowest standard level. It may allow dirty reads. Some databases treat it like READ_COMMITTED.

### READ_COMMITTED

A query sees only committed data. A repeated read can see a newer committed value, so non-repeatable reads may occur. This is a common default in databases such as PostgreSQL and Oracle.

### REPEATABLE_READ

Repeated reads of the same row are protected from non-repeatable changes according to the database's implementation. Phantom behavior differs by database. PostgreSQL's implementation uses snapshot isolation and prevents many anomalies beyond the SQL minimum; MySQL InnoDB has its own locking/MVCC behavior.

### SERIALIZABLE

Strongest standard isolation level. Transactions behave as if executed in a serial order. The database may block or abort transactions to preserve this guarantee. Applications must be ready to retry serialization failures.

### Isolation comparison

| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ_UNCOMMITTED | Possible | Possible | Possible |
| READ_COMMITTED | Prevented | Possible | Possible |
| REPEATABLE_READ | Prevented | Prevented | Depends on DB implementation/standard behavior |
| SERIALIZABLE | Prevented | Prevented | Prevented |

Do not assume identical behavior across database engines. Validate the database's documentation and actual transaction behavior.

## 7. Isolation scenario example

Transaction A reads an account balance of 100. Transaction B updates it to 150 and commits. If A reads again:
- Under READ_COMMITTED, A may see 150.
- Under repeatable-read/snapshot behavior, A may continue to see its original snapshot.
- Under SERIALIZABLE, the database ensures the final outcome is equivalent to some serial execution, possibly by blocking or aborting one transaction.

## 8. Choosing isolation

Use the weakest isolation level that correctly protects the business invariant, with explicit locking or constraints where needed. For inventory, a conditional update can be safer than relying only on a prior read:

```sql
UPDATE inventory
SET quantity = quantity - 1
WHERE product_id = :id AND quantity > 0;
```

Check the affected row count. This prevents quantity from becoming negative when the update is executed atomically by the database.

## 9. Read-only, timeout and rollback-only

- `readOnly = true`: a hint that may enable provider/driver optimizations; do not assume it universally blocks writes.
- `timeout`: requests a transaction timeout; exact enforcement depends on transaction manager/database support.
- rollback-only: marks the current transaction so it cannot commit successfully.
- `@Transactional` defaults: propagation REQUIRED, isolation DEFAULT, readOnly false.

## 10. Transaction boundary design

Keep transactions short. Avoid slow HTTP calls while holding database locks or connections. A local Spring transaction normally cannot make a remote service call atomic with a database update. Use an outbox for reliable event publishing and Saga/compensation for multi-service workflows.

## 11. Common mistakes

- Assuming checked exceptions roll back by default.
- Catching an exception and accidentally committing partial work.
- Expecting self-invocation to trigger a new transactional boundary.
- Using REQUIRES_NEW without understanding extra connection use.
- Assuming NESTED and REQUIRES_NEW are the same.
- Assuming REPEATABLE_READ behaves identically in every database.
- Holding transactions open during remote calls.
- Assuming readOnly prevents every write.
- Assuming @Transactional creates a distributed transaction across microservices.

## 12. Interview questions and answers

**Q1. What is the default rollback rule?** Spring rolls back on RuntimeException and Error by default, but not on checked exceptions.

**Q2. How do you roll back for IOException?** Use `@Transactional(rollbackFor = IOException.class)`.

**Q3. What happens if I catch a RuntimeException?** If it is swallowed and the transaction is not marked rollback-only, the interceptor may see normal completion and commit.

**Q4. REQUIRED vs REQUIRES_NEW?** REQUIRED joins the current transaction or creates one. REQUIRES_NEW suspends the current transaction and starts an independent one.

**Q5. NESTED vs REQUIRES_NEW?** NESTED commonly uses a savepoint inside the existing transaction; REQUIRES_NEW suspends it and commits/rolls back independently.

**Q6. What is dirty read?** Reading data written by another transaction before that transaction commits.

**Q7. READ_COMMITTED vs REPEATABLE_READ?** READ_COMMITTED can return a newer committed value on a repeated read; REPEATABLE_READ protects repeat reads according to database semantics.

**Q8. Does SERIALIZABLE mean no concurrency?** No. The database allows concurrent execution when it can preserve serializable results; it may block or abort conflicting transactions.

**Q9. Why might @Transactional not work?** The call may bypass the Spring proxy, the method/bean may not be eligible, or the wrong transaction manager/resource may be used.

**Q10. Does @Transactional cover a REST call?** No. A local DB transaction cannot normally roll back a remote HTTP operation.

## 13. Practice exercises

1. Create a transfer method and throw RuntimeException after debit. Verify both account updates roll back.
2. Throw a checked IOException. Observe default behavior, then add rollbackFor and compare.
3. Catch a RuntimeException inside a transactional method. Check whether changes commit; then mark rollback-only and compare.
4. Create outer REQUIRED and inner REQUIRES_NEW methods. Commit the inner audit row, then fail the outer transaction. Inspect which rows remain.
5. Run two concurrent transactions that read/update the same row under READ_COMMITTED and REPEATABLE_READ. Observe database-specific behavior.
6. Simulate two buyers reserving the last inventory item. Use a conditional update or locking and verify only one succeeds.

## 14. Quick revision

Rollback rules decide whether a failure rolls back. Propagation decides how a method joins/creates transactions. Isolation controls what concurrent transactions can observe. Know the Spring proxy limitation, checked-exception default, REQUIRED vs REQUIRES_NEW, NESTED savepoints and database-specific isolation behavior.
