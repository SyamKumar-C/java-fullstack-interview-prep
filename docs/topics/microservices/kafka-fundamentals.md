# Apache Kafka Fundamentals

> Priority: P1 | Level: Intermediate | Study time: 60 minutes

## 1. Short interview answer
Kafka is a distributed event log. Producers write records to topics; topics are split into partitions; brokers store partition logs; consumers read records and track offsets. Consumer groups divide partition work. Ordering is guaranteed within a partition, not across a whole topic.

## 2. Core terms
- Broker: Kafka server that stores and serves partition data.
- Topic: named stream of records.
- Partition: ordered log within a topic.
- Offset: record position within one partition.
- Producer: publishes records.
- Consumer: reads records.
- Consumer group: consumers sharing partition work.
- Replication factor: number of partition copies across brokers.

## 3. Keys and ordering
A producer can set a key such as orderId. Records with the same key are normally routed to the same partition by the partitioner, preserving order within that partition.

```java
kafkaTemplate.send("order-events", orderId, event);
```

Use a stable order ID when events for one order must be processed in order. Increasing partition count can change key-to-partition mapping for common partitioners, so plan carefully.

## 4. Consumer groups
If a topic has six partitions:
- One consumer can read all six.
- Three consumers can typically receive two each.
- Six consumers can each receive one.
- More than six consumers in that group leave some consumers idle for this topic.

Different consumer groups can independently read the same topic.

## 5. Delivery guarantees
- At-most-once: commit offset before processing; a crash can lose work.
- At-least-once: process then commit; a crash can cause duplicate processing.
- Kafka exactly-once features help with Kafka-to-Kafka workflows, but do not automatically make external database or HTTP side effects exactly once.

Make consumers idempotent. For database updates, use an event ID with a unique constraint or an inbox/deduplication table in the same transaction as the business update.

## 6. Reliability and retries
Understand producer acknowledgements, retries, idempotence, batching, compression, and delivery timeout. On consumers, understand auto/manual offset commits, polling, session timeout, and max poll interval. Replication and in-sync replicas affect durability. Select settings based on throughput, latency, and failure requirements.

Use bounded retries with delay for transient errors. Send permanent failures to a dead-letter topic with event metadata and failure reason. Infinite immediate retries can block partition progress and create retry storms.

## 7. Kafka vs REST

| Kafka | REST |
|---|---|
| Asynchronous event delivery | Synchronous request/response |
| Consumers can process later | Caller waits for response |
| Retained log supports replay | Usually no built-in replay |
| Useful for event fan-out | Useful for direct queries/commands |
| Adds broker operations | Often simpler operationally |

Use Kafka when decoupling, buffering, replay, or fan-out is valuable—not just to avoid designing an API.

## 8. Common mistakes
- Assuming ordering across partitions.
- Assuming one consumer group means only one application can read a topic.
- Assuming exactly-once covers database writes.
- Committing offsets before work without accepting possible data loss.
- Retrying forever and blocking a partition.
- Using random keys when per-entity ordering is required.

## 9. Interview questions
**Q1. What is a partition?** An ordered log that enables parallelism and distribution.

**Q2. Can two consumers in one group read the same partition at once?** Normally no; a partition is assigned to one consumer in a group at a time.

**Q3. Where is ordering guaranteed?** Within a partition.

**Q4. What if consumers are fewer than partitions?** A consumer can own multiple partitions.

**Q5. Does Kafka guarantee exactly-once database processing?** No. External side effects need idempotency or coordination.

## 10. Practice
1. Design an order-events topic and choose a key.
2. Explain crashes before and after offset commit.
3. Design retry and dead-letter handling.
4. Explain how three services can independently consume the same events.
