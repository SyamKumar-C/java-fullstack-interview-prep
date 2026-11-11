# Cloud Deployment and Observability

A common AWS design may use S3 + CloudFront for static frontend, ALB/API Gateway for entry, ECS/EKS/EC2 for services, RDS for relational data, S3 for objects, SQS/SNS/EventBridge for messaging and Secrets Manager/Parameter Store for configuration.

Choose services based on scale, operations, team skills and cost—not because every system needs every service.

Observability combines structured logs, metrics and distributed traces. Monitor latency percentiles, traffic, error rates, saturation, queue lag and database health.

Use health checks, rolling/blue-green deployment, backward-compatible database changes, rollback plans and secrets outside source control.

**Interview:** Why backward-compatible DB migrations? Old and new app versions may run at the same time during rolling deployments.
