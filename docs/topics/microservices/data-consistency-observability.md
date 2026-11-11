# Data Consistency and Observability

Each service should own its data model. Other services use APIs/events; direct cross-service database writes create tight coupling.

Eventual consistency means different services may temporarily show different views. Design pending states, retries and reconciliation.

## Observability
Logs are discrete events; metrics are numeric measurements over time; traces follow a request across services. Propagate correlation/trace IDs through HTTP and message headers. Avoid logging credentials, tokens or unnecessary personal data.

**Interview:** How debug a request across services? Start with trace/correlation ID, inspect traces, logs and metrics, then identify the slow/failing dependency.
