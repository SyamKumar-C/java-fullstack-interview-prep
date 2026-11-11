# Cascade Types and orphanRemoval

Cascade propagates selected operations: PERSIST, MERGE, REMOVE, REFRESH, DETACH or ALL. It is a lifecycle choice, not a fetch strategy.

orphanRemoval deletes a privately owned child when it is removed from the relationship (at flush).

```java
@OneToMany(mappedBy = "order",
  cascade = CascadeType.ALL, orphanRemoval = true)
private List<OrderLine> lines = new ArrayList<>();
```

Use REMOVE carefully. Deleting an order should not delete a shared Customer referenced by many orders.

**Interview:** Cascade vs orphanRemoval? Cascade propagates entity operations; orphanRemoval deletes a child when disassociated from its owning relationship.
