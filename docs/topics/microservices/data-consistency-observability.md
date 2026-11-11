# Microservice Data Consistency and Observability

## 1. Why consistency is difficult

Each microservice owns its data and usually commits its own local database transaction. A business workflow that updates multiple services cannot normally use one local ACID transaction across all databases.

The design must handle partial failure, retries, duplicate messages and delayed updates.

## 2. Eventual consistency

Eventual consistency means replicas or services may temporarily show different states, but converge when updates are successfully processed. The API and UI should communicate pending or processing states where immediate consistency is not guaranteed.

## 3. Reliable event publishing

The dual-write problem occurs when a service updates its database and separately publishes an event. One action may succeed while the other fails.

The transactional outbox writes business data and an outbox event in the same local database transaction. A publisher later sends the event and marks it delivered. Consumers must still be idempotent because delivery can repeat.

## 4. Idempotency

An operation is idempotent when repeating the same request does not create additional business effects. Use an idempotency key or event ID, store processing state, and enforce uniqueness at the database boundary.

## 5. Saga

A Saga breaks a distributed workflow into local transactions. Each step has a compensating action when a later step fails. Compensation is a business action, not a database rollback; it may not perfectly reverse the real world.

## 6. Observability pillars

- Logs: event details with correlation/trace IDs.
- Metrics: numeric measurements such as request rate, error rate and latency.
- Traces: one request's path across services and dependencies.

Use structured logs and propagate trace context across HTTP and messaging boundaries.

## 7. Important metrics

Track latency percentiles (p50/p95/p99), throughput, error rate, saturation, queue lag, retry count, dead-letter count and dependency health. Alert on user impact, not every noisy internal event.

## 8. Practical scenario

Order service saves an order and outbox event. A publisher sends OrderCreated. Inventory reserves stock; payment authorizes funds. If payment fails, the Saga triggers inventory release and order cancellation. Every consumer handles duplicate events safely.

## 9. Common mistakes

- Assuming message delivery is exactly once end to end.
- Publishing an event before the database transaction commits.
- Retrying forever without backoff or a dead-letter path.
- Logging secrets or personal data.
- Using a trace ID as an idempotency key without a business reason.
- Treating compensation as a guaranteed rollback.

## 10. Interview questions

**Q1. What is eventual consistency?** Services may temporarily disagree but converge after successful propagation.

**Q2. What is the dual-write problem?** A database update and message publish can succeed/fail independently.

**Q3. How does outbox help?** It stores the event in the same local transaction as the business update, then publishes asynchronously.

**Q4. Why are consumers idempotent?** Messages may be delivered more than once.

**Q5. Logs vs metrics vs traces?** Logs describe events, metrics measure behavior, traces show a request path across components.

## 11. Practice

Design an order-created event flow with an outbox table, retry policy, idempotent consumer and dead-letter handling. Explain what happens if the publisher crashes after sending but before marking the row delivered.
