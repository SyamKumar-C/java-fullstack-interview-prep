# Fetch Types, N+1 and Entity Graphs

LAZY loads an association when accessed (provider may use proxies/enhancement). EAGER requires it to be available eagerly, but does not guarantee a SQL join. Prefer choosing fetch plans per use case.

## N+1
One query loads parent rows, then one extra query runs per parent's association. A page of 100 parents can trigger 101 queries.

## Solutions
Use a targeted fetch join, entity graph, DTO projection or batch fetching. Inspect generated SQL and query counts. Be careful with collection fetch joins plus pagination; rows can multiply and provider limitations apply.

```java
@Query("select o from Order o join fetch o.customer where o.id = :id")
Optional<Order> findWithCustomer(long id);
```

**Interview:** Does EAGER mean JOIN? No. It describes fetch availability, not the SQL strategy.
