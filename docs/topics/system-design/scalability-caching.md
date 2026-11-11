# System Design: Scalability, Caching and Load Balancing

Vertical scaling adds resources to one machine; horizontal scaling adds instances. Horizontal scaling needs stateless services or external session state and shared-resource planning.

Load balancers distribute traffic using round-robin, least connections or weighted routing and remove unhealthy instances.

Cache-aside: check cache, load DB on miss, populate cache. Also know write-through and write-behind.

Consider TTL/invalidation, cache stampede, stale data tolerance, key design/tenant isolation, memory limits and eviction.

**Interview:** Why is cache invalidation hard? Cached values become stale when the source changes, and every update path must be considered.
