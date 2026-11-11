# Microservices and Distributed Systems

> **Priority:** P1 | **Level:** Advanced | **Study time:** 3–4 hours

Microservices split a system into independently deployable services organized around business capabilities and data ownership. Splitting code into many services without clear boundaries adds operational cost.

## 1. Service boundaries and ownership

Start with business capabilities, domain boundaries, data ownership, change patterns, and team ownership. A service should own its business rules and data; avoid multiple services writing directly to the same tables.

A shared platform capability (email, file storage, notifications) may become a separate service when it has independent scaling, security, lifecycle, reuse, or operational needs. Otherwise, keep the capability inside the business service to avoid unnecessary network calls and distributed complexity.

## 2. Communication patterns

- Synchronous HTTP/gRPC: caller waits for a response; simple request-response, but creates runtime dependency.
- Asynchronous messaging: producer publishes an event or command; consumers process independently, but need delivery, ordering, retry, and observability design.

## 3. API Gateway and service discovery

An API Gateway can handle edge routing, authentication integration, rate limiting, and request shaping. It should not become the home for all business logic.

Service discovery helps clients locate service instances in dynamic environments. In container platforms, DNS and platform service abstractions often provide this.

## 4. Kafka fundamentals

A topic is divided into partitions. Each partition is an ordered log. Consumer groups distribute partitions among consumers; within a group, a partition is assigned to one consumer at a time.

Ordering is guaranteed within a partition, not across an entire topic. Choose a message key that groups events needing order, such as orderId.

## 5. Delivery semantics and idempotency

Consumers may receive a message more than once. Design handlers to be idempotent, for example by recording processed event IDs or using a unique business key. Exactly-once claims must be understood within the boundaries of the broker and transaction; external database or API side effects need their own coordination.

## 6. Resilience patterns

- Timeout: stop waiting beyond a defined limit.
- Retry: repeat transient failures with bounded attempts and backoff.
- Circuit breaker: temporarily stop calls to a failing dependency.
- Bulkhead: isolate resources so one workload cannot exhaust all capacity.
- Rate limit: control request volume.

Retries can amplify load. Retry only when the operation is safe to retry and use jitter/backoff.

## 7. Saga and transactional outbox

A Saga coordinates a business transaction across services using local transactions and compensating actions. The transactional outbox writes business data and an event record in the same local database transaction; a separate publisher sends the event to the broker.

## 8. Consistency, tracing, and caching

Distributed systems often use eventual consistency. Design APIs and user experience around pending states, retries, and reconciliation where needed.

Use correlation IDs and distributed tracing to follow a request across services. Cache only data with a clear freshness and invalidation strategy.

## 9. Multi-tenancy

A tenant identifier must be validated and propagated through authentication, request handling, database access, logs, and asynchronous messages. Do not trust a client-provided tenant header without checking it against the authenticated identity. Avoid leaking tenant data through caches or shared resources.

## 10. Interview questions

??? question "When should a feature become a separate microservice?"
    Consider independent business ownership, separate scaling or security needs, independent release cadence, reuse across domains, and whether the operational cost is justified. A service should have a clear boundary and owner.

??? question "Why is a database-per-service approach recommended?"
    It preserves service ownership and reduces coupling through shared schemas. Services exchange data through APIs or events rather than directly modifying another service's tables.

??? question "Does Kafka guarantee ordering?"
    Kafka preserves order within a partition. Events with the same key are generally routed to the same partition, which can preserve per-key order. There is no global order across partitions.

??? question "What is the transactional outbox pattern?"
    The service writes its business change and an event row in one database transaction. A publisher later sends the event to the broker, avoiding the dual-write gap between database commit and message publication.

??? question "Why are retries dangerous?"
    Retries can multiply load during an outage and can duplicate side effects. Use timeouts, bounded attempts, backoff with jitter, and idempotent operations.

## 11. Practice

1. Design an order-to-payment Saga.
2. Explain how to avoid duplicate Kafka event processing.
3. Decide whether email should be a shared service or owned by each business service.
4. Design tenant isolation for a multi-tenant Spring Boot application.
