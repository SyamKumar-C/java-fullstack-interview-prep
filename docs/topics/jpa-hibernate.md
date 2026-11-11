# JPA and Hibernate

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 2–3 hours

JPA is a Java persistence specification. Hibernate is a popular implementation of JPA. It maps Java objects to relational database records.

## 1. Entity lifecycle and persistence context

An entity may be transient (not managed), managed, detached, or removed. The persistence context tracks managed entities and their changes.

Dirty checking detects changes to managed entities and synchronizes them during flush. Flush sends pending SQL to the database; commit completes the transaction.

## 2. Relationships and owning side

JPA supports @OneToOne, @OneToMany, @ManyToOne, and @ManyToMany.

In a bidirectional relationship, the owning side controls the foreign-key mapping. The inverse side uses mappedBy. Keep both sides in sync in application code.

## 3. Fetch types and N+1

EAGER requests related data to be fetched eagerly; LAZY defers loading until accessed (subject to provider behavior and persistence-context availability).

The N+1 problem occurs when one query loads parent rows and additional queries are executed for each parent to load related data.

Ways to address it:
- Fetch join in JPQL.
- Entity graphs.
- DTO projections.
- Batch fetching where appropriate.

Do not make every relationship EAGER as a blanket fix.

## 4. Cascade and orphan removal

Cascade propagates selected entity operations from one entity to related entities. orphanRemoval removes a child entity when it is removed from the parent's relationship.

Use cascade only when the lifecycle of the child truly belongs to the parent.

## 5. JPQL, Criteria API, and native SQL

- JPQL queries entities and their mapped fields.
- Criteria API builds queries programmatically.
- Native SQL uses database-specific SQL and can be useful for specialized queries.

## 6. Locking

- Optimistic locking uses a version field, commonly @Version, to detect conflicting updates.
- Pessimistic locking asks the database to lock rows for a transaction.

Choose based on contention and business requirements. Handle optimistic lock conflicts with a retry or user-visible conflict response when appropriate.

## 7. Interview questions

??? question "What is the persistence context?"
    It is the set of managed entity instances associated with an EntityManager. It provides identity tracking, change detection, and synchronization with the database.

??? question "What causes the N+1 query problem?"
    A query loads a list of parent entities, then accessing a lazy relationship causes one additional query per parent. The fix is to choose a suitable fetch plan or projection for the use case.

??? question "What is the difference between flush and commit?"
    Flush synchronizes pending changes in the persistence context to the database within the current transaction. Commit completes the transaction. A flush does not itself guarantee commit.

??? question "What is the difference between CascadeType.REMOVE and orphanRemoval?"
    Cascade REMOVE propagates a remove operation when the parent is removed. orphanRemoval removes a child when it is removed from the parent's relationship.

??? question "How does optimistic locking work?"
    A version column is checked during update. If another transaction has changed the row since it was read, the update affects no matching version and a conflict is raised.

## 8. Practice

1. Model Customer and Order with a many-to-one relationship.
2. Reproduce an N+1 query and fix it using a fetch join.
3. Compare DTO projection with loading full entities.
4. Explain when optimistic locking is preferable to pessimistic locking.
