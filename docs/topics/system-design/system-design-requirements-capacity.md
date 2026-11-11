# System Design: Requirements, Capacity and API Boundaries

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Repeatable design process
Do not start by drawing microservices. First clarify:
1. Functional requirements: what users can do.
2. Non-functional requirements: latency, availability, durability, security, scale, cost.
3. Constraints: team, existing systems, cloud, budget, deadlines.
4. Data model and ownership.
5. APIs and event contracts.
6. Components and data flow.
7. Failure handling and observability.
8. Capacity estimates and bottlenecks.
9. Trade-offs and evolution plan.

State assumptions when exact traffic is unknown.

## 2. Measurable requirements
For an order system:
- Functional: create order, view status, cancel eligible order, pay.
- Non-functional: 99.9% availability, p95 lookup latency below a target, durable records, tenant isolation, auditability.

"Fast" is vague. "p95 under 300 ms at expected peak load" is measurable.

## 3. Capacity estimation
Suppose a service receives 1,000,000 requests per day.

Average requests per second = 1,000,000 / 86,400 = about 11.6.

If peak traffic is five times average, estimate about 58 requests/second at peak. Then estimate payload size, DB reads/writes, storage growth, and concurrency. These are rough estimates; measure real workloads and add headroom.

## 4. API and data ownership
For every operation identify:
- source of truth;
- owning service;
- read/write pattern;
- consistency requirement;
- authorization boundary;
- idempotency and retry behavior.

Avoid two services independently writing the same business entity. Share data through APIs/events or a deliberate read model, not direct cross-service table updates.

## 5. Failure thinking
For each dependency ask:
- What if it is slow or unavailable?
- Can requests be retried safely?
- Is there a timeout?
- Can load be bounded?
- What data may be stale?
- How is failure detected and recovered?

A queue absorbs bursts but does not create unlimited capacity. Define queue limits, consumer throughput, retry policy, and dead-letter handling.

## 6. Interview questions
**Q1. What do you clarify first?** Functional requirements, measurable non-functional requirements, constraints, and expected scale.

**Q2. Why estimate peak traffic?** Average load can hide bursts and bottlenecks.

**Q3. What is a source of truth?** The authoritative owner of a data entity or business fact.

**Q4. Why avoid shared database writes across services?** They couple schemas and deployments and weaken data ownership.

**Q5. What is p95 latency?** 95% of measured requests complete at or below that time; 5% take longer.

## 7. Practice
1. Estimate peak RPS for 2 million daily calls with 4x peak factor.
2. Define requirements for a bulk SMS platform.
3. Identify source of truth for customer, order, and payment.
4. Draw failure flow when a notification provider is unavailable.
