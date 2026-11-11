# Service Discovery, API Gateway and Resilience

## 1. Service discovery

In a distributed system, service instances may start, stop, scale, or move. Hardcoding an instance IP in another service is fragile.

Service discovery lets a caller find healthy service instances through a registry or platform-native DNS/load balancing.

Common models:
- Client-side discovery: caller queries registry and selects an instance.
- Server-side discovery: caller contacts a load balancer that selects an instance.
- Platform discovery: orchestration/DNS such as Kubernetes Services provides stable service names.

## 2. API Gateway

An API Gateway is an entry point for external clients. It can handle cross-cutting edge concerns:
- Routing to backend services.
- Authentication/token validation at the edge.
- Rate limiting and request size limits.
- TLS termination.
- Request/response transformation where justified.
- Access logs and correlation IDs.

Do not move every business rule into the gateway. Domain logic should remain with the service that owns the business capability.

## 3. Load balancing

Load balancing distributes requests across healthy instances. Strategies include round robin, least connections, weighted routing, and latency/health-aware approaches. The best strategy depends on request duration, instance capacity, and session requirements.

## 4. Timeouts and retries

Every remote call should have a timeout. Without one, a caller may hold resources while waiting indefinitely.

Retries help with transient failures, but:
- Retry only operations that are safe to repeat or protected by idempotency.
- Use exponential backoff and jitter.
- Set a retry budget.
- Avoid retrying at every layer, which multiplies load.
- Do not retry permanent errors such as validation failures.

## 5. Circuit breaker

A circuit breaker stops sending calls to a dependency that is failing repeatedly.

States:
- Closed: requests flow; failures are measured.
- Open: calls fail fast or use fallback.
- Half-open: a limited number of test calls check recovery.

A circuit breaker protects the caller and failing dependency from repeated load. It is not a substitute for timeouts or retries.

## 6. Bulkhead and rate limiting

Bulkhead isolation limits how much one dependency or workload can consume. Rate limiting controls request rate. These patterns prevent one noisy client or slow service from exhausting all resources.

## 7. Fallbacks

A fallback should provide a safe, meaningful alternative, such as cached read-only data. Do not return fake success for a payment or order write. A fallback must not hide data loss or violate business correctness.

## 8. Observability

Propagate correlation/trace context across calls. Record latency, error rate, request volume, saturation, and dependency health. Distributed tracing helps connect one user request across services.

## 9. Interview questions and answers

**Q1. Why service discovery?**  
It avoids hardcoded instance addresses and allows callers to find healthy instances as services scale or restart.

**Q2. What belongs in an API Gateway?**  
Edge concerns such as routing, authentication, rate limiting, TLS, and observability. Core domain decisions belong in business services.

**Q3. Why are timeouts essential?**  
They bound how long a call can hold resources and prevent requests from waiting indefinitely.

**Q4. Retry versus circuit breaker?**  
Retry repeats a failed operation under controlled conditions. Circuit breaker temporarily stops calls to a dependency that is failing.

**Q5. What is a bulkhead?**  
Isolation that limits the resources one dependency/workload can consume.

**Q6. Why can retries make an outage worse?**  
They increase traffic to an already failing service, especially when many layers retry or no backoff/jitter is used.

## 10. Practice

Design an API gateway calling order and payment services. Add timeouts, retries only for safe transient failures, a circuit breaker for payment, and a correlation ID propagated across services.

## 11. Quick revision

Discovery finds instances; gateway manages edge traffic. Timeouts bound waiting. Retries need idempotency/backoff. Circuit breakers fail fast; bulkheads isolate resources.
