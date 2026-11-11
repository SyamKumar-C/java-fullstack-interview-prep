# Microservice Communication

## Synchronous
HTTP/REST or gRPC request-response is simple when the caller needs an immediate result. It creates runtime coupling; latency and failures can propagate.

## Asynchronous
Kafka/RabbitMQ-style messaging lets services communicate without requiring consumers to be online at send time. It decouples services but introduces eventual consistency and operational complexity.

## Commands vs events
A command asks a service to do something. An event states that something already happened. Events should describe facts and have stable schemas.

## Reliability
Set timeouts. Retry transient failures with bounded backoff and jitter. Use circuit breakers and bulkheads. Make consumers idempotent; define dead-letter and replay handling.

**Interview:** REST vs messaging? Immediate response and simpler flow vs decoupling and asynchronous processing; decide based on consistency, latency and business needs.
