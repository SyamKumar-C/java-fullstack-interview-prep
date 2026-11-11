# Spring Transactions

Spring's declarative transaction support usually uses AOP proxies and a transaction manager.

```java
@Service
class TransferService {
  @Transactional
  public void transfer(long from, long to, BigDecimal amount) {
    debit(from, amount);
    credit(to, amount);
  }
}
```

By default, Spring rolls back for unchecked exceptions and Error, not every checked exception.

## Propagation
REQUIRED joins or creates; REQUIRES_NEW suspends current and creates independent; SUPPORTS joins if present; MANDATORY requires one; NOT_SUPPORTED suspends; NEVER fails if one exists; NESTED uses savepoints where supported.

## Pitfalls
Self-invocation bypasses proxy interception. Private methods are not normally intercepted by proxy-based @Transactional. Long transactions hold locks/resources. A transaction does not include remote HTTP calls or another service's database automatically. Customize rollback rules with rollbackFor/noRollbackFor.

**Interview:** What is REQUIRES_NEW? An independent transaction, suspending the current one if present.
