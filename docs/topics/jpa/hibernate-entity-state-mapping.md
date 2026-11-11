# JPA Entity States and Mapping

## Entity states
Transient: new object, not managed. Managed: tracked by persistence context. Detached: previously managed, no longer tracked. Removed: marked for deletion. Dirty checking detects changes to managed objects and synchronizes them at flush.

## Mapping
Use @Entity, @Id, @GeneratedValue, @Column, @Table and relationship annotations. Understand schema constraints and generated SQL.

## Relationships
@ManyToOne often owns the foreign key. @OneToMany is commonly inverse. @OneToOne is one-to-one. @ManyToMany uses a join table, but an explicit association entity is often better when the relationship has attributes.

The owning side controls the foreign-key update. Keep both sides of bidirectional relationships consistent in Java.

**Interview:** What is a persistence context? A unit-of-work context tracking managed entities and identity within that context.
