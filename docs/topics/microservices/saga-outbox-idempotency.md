# Saga, Transactional Outbox and Idempotency

A local database transaction cannot automatically make another service's database commit atomic. A cross-service workflow needs explicit coordination.

## Saga
A Saga is a sequence of local transactions. If a later step fails, compensating actions attempt to reverse earlier business effects. Example: reserve stock → authorize payment → confirm order; if payment fails, release stock. Compensation is a new business action, not always a perfect rollback.

## Transactional outbox
Write business data and an outgoing event record in the same local DB transaction. A publisher reads the outbox and sends the event. Publishing and marking must handle retries and duplicates.

## Idempotency
Safely process the same request/message more than once using stable IDs, unique constraints, idempotency keys or processed-message records.

**Interview:** Why outbox? It prevents business data committing while its event is lost (or vice versa) by storing event intent in the same transaction.
