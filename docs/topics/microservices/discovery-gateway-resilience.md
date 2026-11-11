# Service Discovery, API Gateway and Resilience

Service discovery locates healthy instances using DNS, a platform service or registry; it can be client-side or server-side.

An API Gateway centralizes routing, TLS termination, authentication integration, rate limits and request shaping. Avoid moving all business logic into the gateway.

Resilience patterns:
- Timeout: stop waiting after a limit.
- Retry: repeat transient failures carefully.
- Circuit breaker: temporarily stop calls after persistent failures.
- Bulkhead: isolate resources.
- Rate limit: control request volume.

**Interview:** Why not retry every failure? Non-transient errors will not improve; retries can multiply load and duplicate non-idempotent actions. Gateway vs load balancer? Application/API-aware routing vs traffic distribution (features can overlap).
