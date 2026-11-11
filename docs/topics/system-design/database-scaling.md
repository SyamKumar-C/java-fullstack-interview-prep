# System Design: Database Scaling and Reliability

Read scaling: indexes, query tuning, read replicas and caching. Replicas may lag, so consider read-after-write consistency.

Write scaling: partitioning/sharding distributes data. Choose a shard key that spreads load and supports common queries. Hot keys and cross-shard transactions are difficult.

Backups must be tested with restore drills. RPO is acceptable data loss; RTO is acceptable recovery time. Replication improves availability but is not a backup.

**Interview:** Partitioning vs sharding? Partitioning divides a dataset; sharding usually distributes partitions across separate database nodes.
