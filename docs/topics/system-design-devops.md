# System Design, Cloud and DevOps

> **Priority:** P1 | **Level:** Intermediate to advanced | **Study time:** 4–5 hours

System design is about meeting functional and non-functional requirements while balancing cost, complexity, performance, reliability, and security.

## 1. Requirements and capacity estimation

Clarify users, traffic, read/write mix, latency targets, availability, data size, retention, security, and cost. Estimate requests per second, storage growth, bandwidth, and peak load before choosing infrastructure.

## 2. Scaling and load balancing

- Vertical scaling increases resources on one machine.
- Horizontal scaling adds instances.
- A load balancer distributes traffic and may perform health checks.
- Stateless application instances are easier to scale horizontally; keep shared state in suitable external systems.

## 3. Caching

Caching reduces repeated work and latency. Define cache key, TTL, invalidation, consistency, stampede protection, and behavior on cache failure. Cache-aside is a common pattern: read cache, load from database on miss, then populate cache.

## 4. Database scaling

Read replicas can scale read traffic but may lag behind the primary. Partitioning and sharding distribute data; they add routing and operational complexity. Choose a partition key that avoids hot spots and supports access patterns.

## 5. Object storage and file uploads

Object storage such as S3 is suitable for file content. A common design is:
1. Backend authenticates the user and checks upload permission.
2. Backend issues a short-lived pre-signed upload URL.
3. Client uploads directly to object storage.
4. Backend verifies metadata or receives an event and records the file state.
5. Download access is controlled through authorization and short-lived URLs.

Validate file type and size, use safe object keys, scan risky uploads where required, and avoid trusting the filename or content type supplied by the client.

## 6. Service ownership: email, notifications, and reports

Make a capability a separate service when it has independent scaling, security, reuse across domains, lifecycle, or specialized operations. Keep it within a business service when it is tightly coupled to one business workflow and extracting it would add network and operational complexity without enough benefit.

For example, an order service can own the decision to send an order confirmation, while a shared notification platform can own delivery channels, templates, provider integrations, retries, and delivery status.

## 7. Docker and CI/CD

Docker packages an application and its dependencies into an image. CI builds and tests changes; CD automates delivery and deployment. Use immutable versioned images, environment-specific configuration, and secret management.

Deployment strategies:
- Rolling: replace instances gradually.
- Blue-green: switch traffic between two environments.
- Canary: send a small portion of traffic to a new version before increasing it.

## 8. AWS building blocks

- EC2: virtual machines.
- S3: object storage.
- RDS: managed relational databases.
- ECS/EKS: container orchestration.
- CloudFront: content delivery network.
- SES: email sending service.
- Parameter Store / Secrets Manager: configuration and secret storage.

Choose services based on workload, operational skill, security, availability, and cost rather than using every service by default.

## 9. Observability and reliability

- Logs explain individual events.
- Metrics show numerical trends and system health.
- Traces follow requests across services.
- Health checks distinguish process health from readiness to receive traffic.
- Define SLI, SLO, and error budgets for important services.

## 10. Interview questions

??? question "How do you design a scalable REST service?"
    Clarify load and latency requirements, keep app instances stateless, use load balancing, optimize database access, add caching where appropriate, use queues for asynchronous work, and monitor the system. Add capacity based on measured bottlenecks.

??? question "Why use object storage instead of storing files in a relational database?"
    Object storage is designed for large binary objects and can scale independently. The database can store metadata, ownership, status, and object keys while file bytes live in object storage.

??? question "Should email be a separate microservice?"
    It depends. A shared email capability makes sense when several business services need common templates, provider integration, retries, delivery tracking, and independent operations. A simple one-domain notification may remain in that domain until the benefits justify extraction.

??? question "What is the difference between rolling, blue-green, and canary deployment?"
    Rolling replaces instances gradually. Blue-green runs two environments and switches traffic. Canary exposes a small share of traffic to the new version first and expands after checking health.

??? question "What is the difference between logs, metrics, and traces?"
    Logs are event records, metrics are aggregated numerical measurements over time, and traces show the path and timing of a request across components.

## 11. Practice

1. Design a file upload flow using S3 pre-signed URLs.
2. Design an email/notification platform used by several business services.
3. Estimate storage and request volume for a document service.
4. Explain a safe rolling or canary deployment for a Spring Boot API.
