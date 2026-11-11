# Microservice Communication Patterns

## 1. Synchronous communication

A caller waits for a response. Common options include REST/HTTP and gRPC.

Advantages: simple request-response flow and immediate result. Risks: temporal coupling, latency propagation, cascading failures and dependency availability.

Use timeouts, bounded retries, circuit breakers, bulkheads and clear error contracts.

## 2. Asynchronous communication

A producer sends a message/event to a broker and does not wait for every consumer to finish. Examples include Kafka, RabbitMQ and cloud queues.

Advantages: loose timing coupling, buffering and independent processing. Costs: eventual consistency, duplicate delivery, ordering concerns and more operational complexity.

## 3. Command vs event

- Command: asks a specific service to do something, e.g. ReserveInventory.
- Event: states that something happened, e.g. InventoryReserved.

Commands have an intended receiver. Events can have multiple interested consumers.

## 4. Communication choices

| Need | Possible choice |
|---|---|
| Immediate read response | REST/gRPC |
| Notify multiple consumers | Event/message |
| Buffer work during traffic spikes | Queue |
| High-volume ordered event stream | Kafka |
| Request/response with strict latency | Synchronous call with timeout |

## 5. API design

Use versioned, backward-compatible contracts. Define timeouts, error codes, pagination, idempotency and authentication. Avoid sharing database tables between services as a substitute for APIs/events.

## 6. Resilience

- Timeout: stop waiting after a limit.
- Retry: repeat transient failures with bounded exponential backoff and jitter.
- Circuit breaker: temporarily stop calls to an unhealthy dependency.
- Bulkhead: isolate resource pools so one dependency cannot exhaust all capacity.
- Rate limit: control request volume.

Do not retry validation errors or non-idempotent actions blindly.

## 7. Practical scenario

Order service can synchronously call pricing to calculate a quote, but it may publish OrderPlaced for notifications, analytics and fulfillment. Choose based on whether the caller needs an immediate answer and how much coupling is acceptable.

## 8. Common mistakes

- Making long chains of synchronous calls.
- No timeout on downstream requests.
- Retrying every failure.
- Treating events as commands.
- Sharing a database across service boundaries.
- Ignoring schema evolution and duplicate messages.

## 9. Interview questions

**Q1. REST vs messaging?** REST is synchronous request-response; messaging is asynchronous and supports decoupled processing.

**Q2. Command vs event?** A command requests an action; an event records a fact that occurred.

**Q3. Why use Kafka?** Durable high-throughput event streaming, partitioned ordering and consumer groups.

**Q4. What is a timeout?** A bound on how long a caller waits for a dependency.

**Q5. Why idempotency?** To make safe retries possible when outcomes are uncertain.

## 10. Practice

Design communication for order creation, stock reservation, payment, email and reporting. Mark which calls are synchronous and which are events, and explain each choice.
