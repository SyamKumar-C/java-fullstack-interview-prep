# Hibernate Entity States and Mapping

## 1. Entity lifecycle states

A JPA entity commonly moves through four states:

- Transient: new object, not managed and not yet persisted.
- Managed/Persistent: tracked by the persistence context.
- Detached: was managed, but is no longer tracked.
- Removed: marked for deletion; deletion is normally executed at flush/commit.

## 2. State transitions

```java
User user = new User();           // transient
entityManager.persist(user);      // managed
entityManager.flush();            // synchronize changes to DB
entityManager.detach(user);       // detached
entityManager.merge(user);        // returns a managed copy
entityManager.remove(managedUser); // removed
```

Important: merge copies state into a managed instance and returns that instance. The object passed to merge does not become managed merely because merge was called.

## 3. Persistence context and dirty checking

The persistence context is a first-level cache and unit of work. Hibernate tracks managed entity changes and writes updates during flush.

```java
@Transactional
public void rename(long id) {
    User user = repository.findById(id).orElseThrow();
    user.setName("Updated");
    // Dirty checking detects the change; explicit save may not be needed for a managed entity.
}
```

## 4. Flush vs commit

Flush synchronizes pending SQL with the database; commit completes the transaction. Flush can happen before commit, such as before a query under AUTO flush mode. A flush is not the same as a successful commit.

## 5. Mapping basics

- @Entity marks a persistent class.
- @Id defines the primary key.
- @GeneratedValue configures key generation.
- @Column customizes column mapping.
- @ManyToOne / @OneToMany / @OneToOne / @ManyToMany define associations.
- @JoinColumn maps a foreign-key column.

Prefer explicit fetch and cascade choices for relationships.

## 6. Lazy and eager loading

LAZY defers loading until access, where supported. EAGER requires the association to be fetched as part of entity loading semantics, but does not guarantee one SQL join. Fetch plans should be designed per use case to avoid N+1 queries.

## 7. Common mistakes

- Expecting merge to attach the same Java object.
- Calling lazy relationships after the persistence context is closed.
- Assuming save always executes SQL immediately.
- Treating flush as commit.
- Returning entities directly from APIs and triggering accidental lazy loads or serialization cycles.

## 8. Interview questions

**Q1. What is a persistence context?** A set of managed entities tracked by the EntityManager, with identity and dirty checking.

**Q2. persist vs merge?** persist makes a new entity managed; merge copies state into and returns a managed instance.

**Q3. flush vs commit?** Flush sends pending SQL; commit completes the transaction.

**Q4. What is dirty checking?** Provider detects changes to managed entities and writes updates during flush.

## 9. Practice

Load an entity in a transaction, modify it without calling save, and inspect the SQL. Then detach it, modify it and compare behavior before and after merge.
