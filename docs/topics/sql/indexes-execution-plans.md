# Indexes and Execution Plans

An index helps the database find rows without scanning the entire table. B-tree is common, but databases support other index types. Indexes speed some reads but consume storage and add write work.

## Composite indexes
For an index on (a,b), queries filtering by a can often use the leftmost prefix; filtering only by b may not use it efficiently. Exact behavior depends on the database and plan.

## Expression and partial indexes
An expression index stores an expression such as lower(email). A partial index covers only rows matching a predicate where supported.

## EXPLAIN
Inspect scans, joins, estimated/actual rows, sorts and index usage. An existing index is not guaranteed to be used: table size, selectivity, statistics, query shape and cost estimates matter.

**Interview:** Can too many indexes hurt? Yes, they add storage and slow writes.
