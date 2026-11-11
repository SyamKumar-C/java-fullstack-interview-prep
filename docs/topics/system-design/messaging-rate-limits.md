# System Design: Queues, Rate Limiting and Backpressure

Queues absorb bursts and decouple producers/consumers. Define delivery semantics, ordering scope, retry, duplicate handling and lag monitoring.

At-most-once may lose messages; at-least-once can duplicate; exactly-once guarantees are scoped to a system boundary. Design consumers to be idempotent.

Rate-limiting algorithms: fixed window (boundary bursts), sliding window (smoother count), token bucket (controlled bursts with average rate), leaky bucket (smooth output).

Backpressure prevents producers overwhelming consumers using bounded queues, demand signals, rejection policies, load shedding or rate limits.

**Interview:** Why bounded queues? They create an explicit overload boundary instead of unbounded memory growth.
