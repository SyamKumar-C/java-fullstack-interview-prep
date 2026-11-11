# Saga, Transactional Outbox and Idempotency

## 1. Why distributed transactions are difficult

A business operation may update multiple services and databases. A local database transaction cannot normally make changes across all those independent systems atomic.

Example: booking a trip may create a booking, reserve a seat, and charge a payment. If payment fails after the seat is reserved, the system must recover without leaving inconsistent state.

## 2. Saga pattern

A Saga is a sequence of local transactions. Each step commits in its own service. If a later step fails, the system runs compensating actions for completed steps.

Example:
1. Booking service creates PENDING booking.
2. Inventory service reserves a seat.
3. Payment service charges the customer.
4. Booking service marks booking CONFIRMED.

If payment fails, release the seat and mark booking FAILED or CANCELLED.

Compensation is a business action, not a database rollback. A refund is not always identical to erasing a charge; it may be a new financial transaction.

## 3. Choreography versus orchestration

- Choreography: services react to events; no central coordinator.
- Orchestration: a saga orchestrator tells participants which step to execute and handles the workflow state.

Choreography is simple for a small flow but can become hard to trace as event dependencies grow. Orchestration makes the process explicit but introduces a coordinator that must be reliable and observable.

## 4. Transactional outbox

Problem: a service updates its database and publishes an event. If the database commit succeeds but publishing fails, downstream services never learn about the change. If publishing succeeds but the database transaction rolls back, consumers see an event for a change that never committed.

Outbox solution:
1. In one local database transaction, update business data and insert an outbox row.
2. A separate publisher reads unpublished outbox rows.
3. It publishes the event to the broker.
4. It marks the row as published or records delivery state.

This makes the business change and the intent to publish atomic within one database.

## 5. Delivery is usually at least once

The publisher may crash after sending but before marking the row published. It can send the same event again after restart. Therefore consumers must tolerate duplicates.

Use a stable event ID and consumer-side deduplication, often with an inbox/processed-message table and a unique constraint.

## 6. Idempotency

An operation is idempotent when repeating the same request has the same business effect as performing it once.

For payment creation, a client sends an idempotency key. The service stores the key, request identity, and result. A repeated request with the same key returns the original result rather than charging again.

Do not accept the same key for a different payload without a clear conflict response.

## 7. Reliability details

- Use retries with backoff and jitter for transient failures.
- Do not retry permanent validation errors.
- Set timeouts.
- Track saga state and compensation failures.
- Use dead-letter handling for messages that cannot be processed.
- Make consumers idempotent.
- Include correlation IDs and event IDs in logs.
- Design compensation to be safe when repeated.

## 8. Interview questions and answers

**Q1. What is a Saga?**  
A distributed workflow made of local transactions with compensating actions for failures.

**Q2. Choreography versus orchestration?**  
Choreography uses events and independent reactions. Orchestration uses a coordinator to manage the workflow steps and state.

**Q3. What problem does the outbox solve?**  
It prevents the database-update/message-publish dual-write gap by recording the event intent in the same local transaction as the business update.

**Q4. Does the outbox guarantee exactly-once delivery?**  
No. Duplicate publication is possible. Consumers must be idempotent or deduplicate using stable event IDs.

**Q5. What is an idempotency key?**  
A client-provided or generated identifier used to recognize retries of the same logical request and avoid repeating its business effect.

**Q6. Is compensation a rollback?**  
No. It is a new business operation that semantically reverses or offsets a previous step, such as releasing inventory or issuing a refund.

## 9. Practice

Design an order workflow with inventory and payment services. Draw the success path and payment-failure compensation path. Add an outbox to the order service and deduplication to the inventory consumer.

## 10. Quick revision

Saga coordinates local transactions. Outbox atomically stores business change plus event intent. Delivery can duplicate. Idempotency makes retries safe.
