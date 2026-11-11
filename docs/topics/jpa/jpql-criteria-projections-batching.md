# JPA Queries, Projections, Pagination and Batching

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Short interview answer
Spring Data JPA supports derived queries, JPQL, native SQL, Criteria API, projections, pagination, and batch operations. Choose the simplest readable query that produces efficient SQL. Inspect generated SQL for complex or large queries.

## 2. Query choices

| Query | Uses | Best fit |
|---|---|---|
| Derived method | Entity properties | Simple filters |
| JPQL | Entity names and fields | Portable object queries |
| Native SQL | Database tables and SQL | Vendor features or tuned SQL |
| Criteria API | Programmatic query model | Dynamic filters |

```java
@Query("""
    select o from Order o
    where o.customer.id = :customerId
      and o.status = :status
    order by o.createdAt desc
    """)
List<Order> findOrders(long customerId, OrderStatus status);
```

JPQL uses entity names and Java properties, not table/column names.

## 3. Projections
Projections load only fields needed by a read API.

```java
public record OrderSummaryDto(Long id, BigDecimal total) {}

@Query("""
    select new com.example.OrderSummaryDto(o.id, o.total)
    from Order o where o.customer.id = :customerId
    """)
List<OrderSummaryDto> summaries(long customerId);
```

Interface projections are also supported. Projections reduce selected data and avoid returning managed entities directly from controllers.

## 4. Pagination

```java
Page<Order> findByStatus(OrderStatus status, Pageable pageable);

Pageable pageable = PageRequest.of(
    0, 20, Sort.by(Sort.Direction.DESC, "createdAt")
);
Page<Order> page = repository.findByStatus(PAID, pageable);
```

Page commonly needs a count query. Slice can avoid total-count calculation when only the existence of a next slice matters. Deep OFFSET pagination can be expensive; keyset pagination is often better for large scrolling results. Whitelist client-sortable fields.

## 5. Criteria API

```java
CriteriaBuilder cb = entityManager.getCriteriaBuilder();
CriteriaQuery<Order> cq = cb.createQuery(Order.class);
Root<Order> order = cq.from(Order.class);

List<Predicate> predicates = new ArrayList<>();
if (status != null) {
    predicates.add(cb.equal(order.get("status"), status));
}
if (fromDate != null) {
    predicates.add(cb.greaterThanOrEqualTo(order.get("createdAt"), fromDate));
}
cq.where(predicates.toArray(Predicate[]::new));
cq.orderBy(cb.desc(order.get("createdAt")));

List<Order> result = entityManager.createQuery(cq)
    .setFirstResult(0).setMaxResults(20).getResultList();
```

Keep dynamic predicate building in a repository/specification layer and test combinations.

## 6. Batch writes

```java
@Transactional
public void importRows(List<InputRow> rows) {
    int batchSize = 50;
    for (int i = 0; i < rows.size(); i++) {
        entityManager.persist(map(rows.get(i)));
        if ((i + 1) % batchSize == 0) {
            entityManager.flush();
            entityManager.clear();
        }
    }
}
```

Flush sends pending SQL to the database inside the transaction. It does not commit. Clear detaches managed entities and controls persistence-context memory. After clear, previously loaded entities are detached. Hibernate batching depends on configuration, JDBC driver, and ID generation strategy; verify actual SQL. Identity ID generation can prevent insert batching in some configurations.

## 7. Common mistakes
- Returning entities with large lazy relationships from controllers.
- Using Page when total count is not needed.
- Paginating collection fetch joins without checking SQL and ORM warnings.
- Assuming saveAll always means one JDBC batch.
- Assuming flush commits.
- Concatenating untrusted sort strings into SQL.

## 8. Interview questions
**Q1. JPQL vs native SQL?** JPQL queries the entity model; native SQL queries database tables and supports vendor features.

**Q2. What is a projection?** A partial view of selected fields.

**Q3. Page vs Slice?** Page includes total count; Slice mainly indicates whether another slice exists.

**Q4. Does flush commit?** No. It synchronizes pending changes inside the current transaction.

**Q5. Why clear during batch processing?** To detach entities and limit persistence-context memory.

## 9. Practice
1. Write one filter as derived query and JPQL.
2. Return an order DTO projection.
3. Compare Page and Slice SQL.
4. Import 10,000 rows in batches and measure memory/query count.
