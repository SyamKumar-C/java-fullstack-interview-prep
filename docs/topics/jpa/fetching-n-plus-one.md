# JPA Fetching Strategies and the N+1 Query Problem

## 1. Entity fetching

JPA relationships can be loaded eagerly or lazily.

- EAGER: the provider must fetch the association as part of loading the entity, though it may use joins or separate queries.
- LAZY: the association can be fetched when it is accessed. It may be implemented using proxies or enhancement.

Fetch type is not the same as SQL join strategy. LAZY does not guarantee one later query, and EAGER does not guarantee one join.

## 2. N+1 problem

Suppose one query loads 100 orders, then application code accesses each order's customer. If customers are not already available, the ORM may execute one query for orders plus one query per order: 1 + N queries.

```java
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    System.out.println(order.getCustomer().getName());
}
```

This can create many database round trips and slow the endpoint.

## 3. Join fetch

JPQL can fetch an association in the same query:

```java
@Query("select o from Order o join fetch o.customer")
List<Order> findAllWithCustomer();
```

This is useful when the endpoint needs the customer for every returned order. Be careful when join-fetching multiple collection associations because rows multiply and pagination can become incorrect or inefficient.

## 4. EntityGraph

An EntityGraph specifies which associations should be fetched for a particular use case.

```java
@EntityGraph(attributePaths = {"customer"})
List<Order> findByStatus(OrderStatus status);
```

This can keep the repository method readable and avoid changing the mapping's global fetch strategy.

## 5. Batch fetching

Batch fetching loads several lazy associations in a smaller number of queries instead of one query per parent. Hibernate supports batch-size configuration. It reduces round trips but is not identical to a join fetch.

## 6. DTO projections

If an API needs only a few columns, query directly into a DTO/projection instead of loading a full entity graph. This reduces transferred data and avoids accidental lazy loading during serialization.

## 7. Pagination concerns

Collection fetch joins can multiply SQL rows. Applying pagination to such results may produce warnings, in-memory pagination, duplicates, or unexpected results depending on provider and query. A common pattern is:
1. Page the root entity IDs.
2. Fetch required associations for those IDs in a second query.
3. Restore the page order if necessary.

## 8. LazyInitializationException

Accessing an uninitialized lazy association after the persistence context is closed can cause LazyInitializationException. Do not solve this by making every relationship EAGER. Fetch what the use case needs inside the transaction, then map to DTOs.

## 9. Interview questions and answers

**Q1. What is the N+1 problem?**  
One query loads N parent rows, then accessing a related association triggers N additional queries.

**Q2. How do you fix it?**  
Use a join fetch, EntityGraph, batch fetching, or DTO projection based on the use case and result shape.

**Q3. Is EAGER the solution?**  
No. It can fetch too much and still use extra queries. Prefer use-case-specific fetch plans.

**Q4. Difference between join fetch and EntityGraph?**  
Join fetch is explicit in JPQL. EntityGraph declares fetch requirements separately from the query; the provider chooses how to satisfy them.

**Q5. Why are collection fetch joins risky with pagination?**  
A parent can appear in multiple SQL rows because of collection elements, making row-level pagination differ from parent-level pagination.

**Q6. What causes LazyInitializationException?**  
Code accesses an uninitialized lazy association after the persistence context is no longer available.

## 10. Practice

Load a page of orders and display customer name plus item count. Compare a join fetch, EntityGraph, and DTO projection. Explain which approach avoids loading unused entity fields and how you would preserve pagination.

## 11. Quick revision

N+1 = one root query plus one query per accessed association. Fetch per use case. Avoid global EAGER mappings and be careful with collection fetch joins plus pagination.
