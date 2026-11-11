# JPA Cascades and Orphan Removal

## 1. Entity relationships

JPA relationships describe how entities refer to one another: @OneToOne, @OneToMany, @ManyToOne and @ManyToMany. The owning side controls the foreign-key or join-table update. In a bidirectional relationship, the inverse side uses mappedBy.

## 2. Cascade

Cascade controls which entity operations are propagated from a parent to related entities.

- PERSIST: persist child when parent is persisted.
- MERGE: merge child when parent is merged.
- REMOVE: remove child when parent is removed.
- REFRESH: refresh child when parent is refreshed.
- DETACH: detach child when parent is detached.
- ALL: all cascade operations above.

```java
@OneToMany(mappedBy = "order", cascade = CascadeType.PERSIST)
private List<OrderItem> items = new ArrayList<>();
```

Use cascade only when the child lifecycle is truly controlled by the parent. CascadeType.ALL is not a safe default for every relationship.

## 3. Orphan removal

`orphanRemoval = true` tells the provider to remove a child entity when it is removed from the relationship, subject to the relationship and persistence-context rules.

```java
@OneToMany(mappedBy = "order",
           cascade = CascadeType.ALL,
           orphanRemoval = true)
private List<OrderItem> items = new ArrayList<>();
```

Removing an item from the managed collection can schedule deletion of that child. It is useful for privately owned children such as order lines.

## 4. Cascade REMOVE vs orphanRemoval

Cascade REMOVE applies when the parent is removed. Orphan removal applies when a child is disassociated from the parent relationship. They solve different lifecycle events.

## 5. Owning side

For a bidirectional relationship, update both sides in helper methods so the in-memory model remains consistent.

```java
public void addItem(OrderItem item) {
    items.add(item);
    item.setOrder(this);
}
```

## 6. Practical scenarios

- Order -> OrderItem: child is often privately owned; cascade persist and orphan removal may fit.
- Employee -> Department: department is shared; deleting an employee should not delete the department.
- User -> Role: roles are shared; cascading remove is usually dangerous.

## 7. Common mistakes

- Applying CascadeType.ALL to shared entities.
- Updating only the inverse side and expecting the foreign key to change.
- Confusing cascade remove with orphan removal.
- Removing a child from a collection without maintaining the owning side.
- Using many-to-many with lifecycle ownership when an explicit join entity is more suitable.

## 8. Interview questions

**Q1. What is cascade?** It propagates selected entity operations from one entity to related entities.

**Q2. CascadeType.ALL meaning?** PERSIST, MERGE, REMOVE, REFRESH and DETACH.

**Q3. orphanRemoval vs CascadeType.REMOVE?** Orphan removal deletes a child when disassociated; cascade remove deletes children when the parent is removed.

**Q4. What is the owning side?** The side responsible for updating the relationship mapping, commonly the side containing the foreign key.

## 9. Practice

Model Order and OrderItem. Add/remove helper methods, configure cascade and orphan removal, and test what SQL/deletions occur when an item is removed and when the order is deleted.
