# AWS Deployment and Observability for Java Applications

## 1. Typical deployment architecture

A common setup for an Angular + Spring Boot system:
- Angular production files are built and stored in S3.
- CloudFront serves the static frontend through a CDN.
- Spring Boot runs on EC2, ECS or Kubernetes/EKS.
- An Application Load Balancer routes traffic to healthy backend instances.
- RDS hosts a managed relational database.
- S3 stores uploaded files and generated reports.
- CloudWatch collects logs, metrics and alarms.
- Secrets Manager or Systems Manager Parameter Store stores runtime configuration/secrets.

Choose services based on scale, operations skill, availability needs and cost; Kubernetes is not automatically required.

## 2. Deployment flow

1. CI checks out source and runs tests.
2. Build frontend/backend artifacts.
3. Scan dependencies and artifacts.
4. Publish Docker image or static files.
5. Deploy to a test/staging environment.
6. Run smoke/integration checks.
7. Promote to production using rolling, blue-green or canary deployment.
8. Monitor health and roll back if required.

## 3. Compute choices

- EC2: full VM control; you manage OS, patching and deployment.
- ECS: managed container orchestration with less cluster overhead.
- EKS: managed Kubernetes control plane; useful when Kubernetes capabilities/ecosystem are required, but adds operational complexity.
- Lambda: event-driven/serverless workloads with execution/runtime constraints.

## 4. Static frontend hosting

For Angular on S3 + CloudFront:
- Keep the bucket private and use CloudFront Origin Access Control.
- Configure SPA fallback so client-side routes resolve to index.html.
- Use hashed asset filenames and long cache lifetimes for immutable assets.
- Give index.html a short/no-cache policy so users receive new deployment references.
- Invalidate CloudFront paths when needed; avoid invalidating everything by default.

## 5. Backend reliability

Use multiple instances across availability zones, health checks, autoscaling and graceful shutdown. Keep sessions stateless where possible or use a shared session store. Store uploaded files in S3 rather than local instance disk when instances are replaceable.

## 6. Observability

- Logs: structured JSON with request/correlation IDs; never log passwords or tokens.
- Metrics: request count, latency percentiles, error rate, CPU/memory, pool usage, queue lag and database connections.
- Traces: follow a request across gateway, services, database and messaging.
- Alerts: page on customer-impacting symptoms and actionable thresholds.

## 7. Secrets and security

Use IAM roles instead of long-lived access keys where possible. Store secrets outside source control, rotate them, use least privilege, enforce TLS and restrict network access. Separate configuration by environment.

## 8. Practical scenario

A new Spring Boot version causes 5xx errors. A canary deployment exposes the issue to a small share of traffic; metrics show error rate rising. Stop rollout and route traffic back to the stable version. Database schema changes must remain compatible with both versions during the rollback window.

## 9. Common mistakes

- Putting AWS credentials in application.yml or Git.
- Making S3 upload buckets public unnecessarily.
- Caching index.html for too long.
- Deploying a breaking database migration before application compatibility is ready.
- Logging sensitive data.
- Using Kubernetes without a clear need.
- Treating a successful deployment as proof the application is healthy.

## 10. Interview questions

**Q1. S3 vs CloudFront?** S3 stores objects; CloudFront caches and delivers content through edge locations.

**Q2. ECS vs EKS?** ECS is AWS-native container orchestration; EKS provides managed Kubernetes.

**Q3. How do you deploy without downtime?** Use health-checked rolling/blue-green/canary deployment and backward-compatible database changes.

**Q4. How do you manage secrets?** Use managed secret/config stores and IAM roles; do not commit secrets.

**Q5. What do you monitor?** Latency, errors, throughput, saturation, dependency health and business metrics.

## 11. Practice

Design deployment for Angular on S3/CloudFront and Spring Boot on ECS behind an ALB with RDS, S3 uploads, Parameter Store and CloudWatch. Explain rollback and cache invalidation.
