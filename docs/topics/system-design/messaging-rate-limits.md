# Messaging, Queues and Rate Limiting

## 1. Why messaging?

A queue or event broker decouples producers from consumers, buffers bursts and supports asynchronous work. It also introduces delivery, ordering, retry and duplicate-processing concerns.

## 2. Queue vs event stream

A work queue commonly distributes tasks among workers; a message is normally processed by one competing consumer. An event stream such as Kafka retains ordered records per partition and allows multiple consumer groups to read independently.

Choose based on retention, replay, ordering, throughput and processing model.

## 3. Delivery semantics

- At-most-once: a message may be lost but is not redelivered.
- At-least-once: message is retried until acknowledged; duplicates are possible.
- Exactly-once claims are scoped to specific systems/transactions; end-to-end business effects still require careful design and idempotency.

## 4. Retry and dead-letter handling

Use bounded retries, exponential backoff and jitter for transient errors. Send messages that exceed retry policy to a dead-letter queue/topic for inspection and controlled replay. Do not retry permanent validation failures forever.

## 5. Ordering

Ordering is usually scoped to a queue or partition. In Kafka, records with the same key are routed to the same partition, preserving partition order. More partitions increase parallelism but do not provide global ordering.

## 6. Backpressure

Backpressure prevents producers from overwhelming consumers. Use bounded queues, consumer concurrency limits, rate limits, pause/resume or load shedding. Monitor queue depth and age of oldest message.

## 7. Rate limiting algorithms

- Fixed window: simple counter per time window; can allow bursts at boundaries.
- Sliding window: smoother counting over time.
- Token bucket: tokens refill at a rate; allows controlled bursts.
- Leaky bucket: processes at a steady rate.

Rate limits can be per user, API key, tenant, IP or endpoint. Distributed systems need shared state or a gateway/service that coordinates limits.

## 8. Practical scenario

A report-generation API receives bursts. It validates and stores a job, returns 202 Accepted with job ID, and publishes work to a queue. Workers process with bounded concurrency, update status, retry transient failures and send poison messages to a DLQ.

## 9. Common mistakes

- Assuming messages are delivered exactly once to business logic.
- No idempotency key or duplicate detection.
- Unbounded retries and queues.
- Ignoring poison messages.
- Expecting global ordering across partitions.
- Returning 200 when asynchronous work has only been accepted.

## 10. Interview questions

**Q1. Queue vs Kafka?** A work queue distributes tasks; Kafka is a retained partitioned event log supporting replay and multiple consumer groups.

**Q2. At-least-once means what?** Messages may be delivered repeatedly, so consumers must be idempotent.

**Q3. What is a DLQ?** A place for messages that could not be processed after configured attempts.

**Q4. Token bucket?** A rate limiter that refills tokens over time and permits requests when tokens are available.

**Q5. What is backpressure?** A way to slow or limit producers when consumers cannot keep up.

## 11. Practice

Design a report job pipeline with 202 response, queue, worker pool, retry policy, DLQ, job status API and per-tenant rate limits.
