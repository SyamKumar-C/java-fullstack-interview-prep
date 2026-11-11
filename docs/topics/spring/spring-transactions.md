# Spring Transactions

## 1. What is a transaction?

A transaction groups database operations into one logical unit. The usual ACID properties are:
- Atomicity: all operations commit or none do.
- Consistency: constraints and rules remain valid.
- Isolation: concurrent transactions do not see invalid intermediate states beyond the chosen isolation level.
- Durability: committed data survives failures according to the database's guarantees.

## 2. @Transactional

Spring's `@Transactional` declares that a method should run within a transaction managed by Spring.

```java
@Service
class TransferService {
    @Transactional
    public void transfer(long fromId, long toId, BigDecimal amount) {
        accountRepository.debit(fromId, amount);
        accountRepository.credit(toId, amount);
    }
}
```

If a configured rollback condition occurs, both database changes roll back. The transaction manager and resource integration must be configured correctly.

## 3. Proxy-based behavior

In common Spring proxy mode, calls pass through a proxy that starts/joins the transaction around the method. A self-invocation such as `this.otherTransactionalMethod()` bypasses the proxy, so the inner method's transactional annotation may not be applied independently.

Put transaction boundaries on methods called through the Spring proxy, or refactor the transactional operation into another bean. AspectJ mode has different behavior.

## 4. Rollback rules

By default, Spring rolls back on unchecked exceptions (RuntimeException and Error), but not on checked exceptions.

```java
@Transactional(rollbackFor = IOException.class)
public void importData() throws IOException {
    // ...
}
```

Use rollbackFor/noRollbackFor only when the business contract requires it. Catching an exception and not rethrowing it can prevent the interceptor from seeing a rollback-triggering failure unless the transaction is explicitly marked rollback-only.

## 5. Propagation

Common propagation modes:
- REQUIRED (default): join an existing transaction or create one.
- REQUIRES_NEW: suspend the current transaction and create a new one.
- SUPPORTS: join if one exists; otherwise run without a transaction.
- MANDATORY: require an existing transaction.
- NOT_SUPPORTED: suspend an existing transaction and run without one.
- NEVER: fail if a transaction exists.
- NESTED: use a nested transaction/savepoint when supported by the transaction manager.

REQUIRES_NEW can consume another database connection while the outer transaction is suspended. Use it carefully.

## 6. Isolation

Common levels:
- READ_UNCOMMITTED
- READ_COMMITTED
- REPEATABLE_READ
- SERIALIZABLE

The exact behavior and supported levels depend on the database. Isolation addresses phenomena such as dirty reads, non-repeatable reads, and phantom reads. Stronger isolation can reduce concurrency or cause serialization failures that the application must handle.

## 7. Read-only and timeout

`readOnly=true` is generally a hint to the transaction manager/provider and may enable optimizations. Do not assume it universally blocks writes at the database level. A timeout can bound how long a transaction is allowed to run, subject to transaction manager and database support.

## 8. Transaction boundary design

Keep transactions focused and short. Avoid remote HTTP calls while holding database locks. A database transaction cannot atomically include a remote service call; use an outbox or Saga for distributed workflows.

## 9. Common mistakes

- Expecting self-invocation to trigger a proxy.
- Assuming checked exceptions cause rollback by default.
- Using REQUIRES_NEW without understanding connection usage.
- Holding transactions open during slow remote calls.
- Assuming readOnly prevents all writes.
- Catching exceptions and accidentally committing partial work.
- Expecting one @Transactional annotation to create a distributed transaction across services.

## 10. Interview questions and answers

**Q1. How does @Transactional work?**  
In common proxy mode, Spring wraps method calls with transaction advice that begins or joins a transaction, commits on success, and rolls back according to configured rules.

**Q2. Why may @Transactional not work on a self-invoked method?**  
The call does not pass through the Spring proxy, so the transactional interceptor is bypassed.

**Q3. Default rollback behavior?**  
Rollback for RuntimeException and Error; checked exceptions do not trigger rollback by default.

**Q4. REQUIRED versus REQUIRES_NEW?**  
REQUIRED joins the current transaction or creates one. REQUIRES_NEW suspends the current transaction and starts an independent one.

**Q5. What is isolation?**  
It controls how concurrent transactions observe each other's changes and which read anomalies are allowed.

**Q6. Can @Transactional make a REST call and DB update atomic?**  
No. The local database transaction cannot normally roll back a remote HTTP operation. Use distributed workflow patterns such as Saga and outbox.

## 11. Practice

Create a transfer service. Test rollback when crediting the destination fails. Then move a method into another bean to demonstrate the proxy/self-invocation difference.

## 12. Quick revision

Spring transaction annotations are commonly proxy-based. Know rollback defaults, propagation, isolation, and why remote calls should not be held inside long DB transactions.
