# SQL Transactions and Normalization

## ACID
Atomicity: all or nothing. Consistency: constraints remain valid. Isolation: concurrent work follows the isolation level. Durability: committed data survives failures according to database guarantees.

## Isolation anomalies
Dirty read: see uncommitted data. Non-repeatable read: same row changes within a transaction. Phantom read: repeated predicate query returns a different set. Lost update: one update overwrites another.

Exact prevention varies by database and isolation implementation.

## Normalization
1NF: atomic values/no repeating groups. 2NF: no partial dependency on part of a candidate key. 3NF: no problematic transitive dependency of non-key attributes on a key. BCNF: every determinant is a candidate key.

Normalization reduces update anomalies. Denormalization is a measured read-performance trade-off.

**Interview:** Why denormalize? To reduce joins or improve reads when the trade-off is understood.
