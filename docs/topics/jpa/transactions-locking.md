# JPA Transactions, Flush and Locking

Flush synchronizes pending persistence-context changes to the database. Commit completes the transaction. Flush can occur before commit, for example before a query or explicit flush().

## Optimistic locking
Use @Version. The update checks the version; if another transaction changed the row, the expected update fails and an optimistic locking exception is raised.

## Pessimistic locking
A query can request a database lock, such as LockModeType.PESSIMISTIC_WRITE. It can block other transactions and should be used carefully.

**Interview:** Does save() always execute SQL immediately? Not necessarily; SQL may be delayed until flush/commit depending on provider and ID strategy. Optimistic detects conflicts; pessimistic acquires locks.
