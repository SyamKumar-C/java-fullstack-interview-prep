# Designing File Storage, Email and Notification Capabilities

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Short interview answer
A shared capability should become a separate service when it needs independent ownership, scaling, security, reuse, or lifecycle. A business service should keep business decisions. For example, the order service decides when an order confirmation is needed; a notification service can own templates, delivery channels, retries, and provider integration.

## 2. Decide ownership
Ask:
1. Who owns the business rule?
2. Is the capability reused by many domains?
3. Does it need a different scaling or security boundary?
4. Does it have independent data and lifecycle?
5. Does separation reduce coupling enough to justify network and operational cost?

Do not create a microservice for every shared utility. Each service adds deployment, monitoring, security, failure, and support work.

## 3. File storage flow
A business service can own file metadata and authorization while using object storage directly.

1. Client requests an upload.
2. API checks permission and creates a PENDING file record.
3. API returns a short-lived pre-signed upload URL.
4. Client uploads bytes directly to object storage.
5. Backend validates size, type, and checksum.
6. Metadata becomes AVAILABLE.
7. Downloads use an authorized short-lived URL or backend proxy.

Store tenant ID, owner ID, object key, content type, size, checksum, status, and retention policy. Never expose bucket credentials to clients.

A dedicated file service is useful when many domains need common scanning, retention, audit, lifecycle, or access policies. It should not own order/customer business rules.

## 4. Email and notifications
The business service publishes an intent/event such as OrderPlaced. The notification capability can select channel/template, render content, send through SES/provider, retry transient failures, record delivery status, and apply user preferences/rate limits.

Use an outbox to publish reliably after the business transaction commits. Delivery is usually at-least-once, so design for duplicates where they matter.

## 5. Reports
A simple domain report may belong to its business service. A shared reporting platform makes sense for common scheduling, export formats, long-running jobs, access control, and delivery across domains.

For large reports: accept request and return job ID; generate asynchronously; store output in object storage; expose job status; issue expiring download link; check tenant/user authorization on every request.

## 6. Decision matrix

| Factor | Keep in business service | Separate service |
|---|---|---|
| Business rules | Domain-specific | Shared technical workflow |
| Reuse | One domain | Many domains |
| Scaling | Similar workload | Very different workload |
| Security | Same trust boundary | Central policy/audit needed |
| Lifecycle | Changes with domain | Independent ownership |
| Complexity | Fewer moving parts | Separation benefit outweighs network cost |

## 7. Common mistakes
- Letting notification service decide whether an order qualifies for confirmation.
- Moving all file authorization to a generic service without domain ownership.
- Making email delivery part of the critical order transaction.
- Assuming an email call can roll back with a database transaction.
- Returning permanent public object URLs.
- Forgetting tenant ID in object metadata and authorization.

## 8. Interview questions
**Q1. Should S3 always be a separate microservice?** No. A business service can use object storage directly. Separate shared file management only when reuse, policy, security, or lifecycle justify it.

**Q2. Who decides when an email is sent?** The business domain; notification owns delivery mechanics.

**Q3. Why use an outbox?** It avoids two unreliable independent actions: committing a database change and publishing an event.

**Q4. When should reporting be separate?** When it is shared, long-running, resource-heavy, scheduled, or independently governed.

**Q5. What is the cost of a microservice?** Network failures, deployments, observability, security, data consistency, and support.

## 9. Practice
1. Design an invoice upload flow with pre-signed URLs.
2. Decide ownership for order-confirmation email.
3. Design an asynchronous report job with tenant-safe downloads.
4. Explain when a shared library is better than a service.
