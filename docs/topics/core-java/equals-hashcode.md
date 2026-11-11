# equals() and hashCode() in Java

## 1. Why these methods matter

Every Java class inherits `equals()` and `hashCode()` from `Object`. They define logical equality and support hash-based collections such as `HashMap` and `HashSet`.

- `==` compares primitive values, or object references.
- `equals()` compares logical equality when a class overrides it.
- `hashCode()` returns an integer used to select a hash bucket.

## 2. The equals contract

An implementation must be:

1. Reflexive: `x.equals(x)` is true.
2. Symmetric: if `x.equals(y)`, then `y.equals(x)`.
3. Transitive: if x equals y and y equals z, x equals z.
4. Consistent: repeated calls return the same result while relevant state is unchanged.
5. Non-null: `x.equals(null)` is false.

## 3. The hashCode contract

- If two objects are equal according to `equals`, they **must** have the same hash code.
- Unequal objects may have the same hash code. This is a collision, not a contract violation.
- A hash code should remain stable while fields used in equality remain unchanged.

The reverse implication is false: same hash code does not mean equal objects.

## 4. Correct implementation

For a simple immutable value object:

```java
import java.util.Objects;

final class EmployeeKey {
    private final long id;
    private final String department;

    EmployeeKey(long id, String department) {
        this.id = id;
        this.department = department;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof EmployeeKey other)) return false;
        return id == other.id
                && Objects.equals(department, other.department);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, department);
    }
}
```

For Java versions before pattern matching for `instanceof`, use an explicit cast after the type check.

## 5. How HashMap uses both methods

Simplified lookup:

1. HashMap calls `hashCode()` to calculate a hash.
2. It maps that hash to a bucket.
3. It checks entries in that bucket.
4. It compares stored and requested keys using hash and `equals()`.
5. It returns the matching value or reports no match.

If equal keys produce different hash codes, they may go to different buckets. HashMap may never compare them with `equals()`, so lookup can fail.

## 6. Mutable key problem

Do not change fields used by `equals` or `hashCode` after inserting a key into a HashMap.

```java
class Key {
    int id;
    Key(int id) { this.id = id; }
    public int hashCode() { return Integer.hashCode(id); }
    public boolean equals(Object o) {
        return o instanceof Key k && id == k.id;
    }
}
```

If the key's id changes after insertion, lookup may search a different bucket from the one where the entry was stored. Prefer immutable keys.

## 7. Records

A Java record automatically provides value-based `equals()`, `hashCode()`, and `toString()` using its components:

```java
record EmployeeKey(long id, String department) {}
```

This is useful for DTOs and value objects when the generated equality matches the domain meaning.

## 8. Common mistakes

- Overriding `equals` but not `hashCode`.
- Comparing strings with `==`.
- Including mutable fields in equality for map keys.
- Using every field without considering the domain identity.
- Assuming hash collisions mean a broken implementation.
- Writing equality that is not symmetric across subclasses.

## 9. Interview questions and answers

**Q1. Why must equal objects have the same hash code?**  
Hash-based collections use the hash to locate a bucket before checking equality. Equal objects must reach the same bucket for lookup to work.

**Q2. Can two unequal objects have the same hash code?**  
Yes. The hash code has a limited integer range, so collisions are expected and handled by the collection.

**Q3. What happens if only equals() is overridden?**  
The class inherits Object's identity-based hashCode. Two logically equal instances can have different hash codes, breaking HashMap and HashSet behavior.

**Q4. Why should a HashMap key be immutable?**  
Changing equality-related state can change its hash after insertion. The entry remains in its original bucket, while later lookup searches using the new hash.

**Q5. What is the difference between identity and value equality?**  
Identity means two references point to the same object. Value equality means two objects represent the same logical value.

## 10. Coding exercises

1. Create a Product value object whose identity is SKU. Put two objects with the same SKU into a HashSet; the set should contain one element.
2. Implement a composite key using tenantId and externalId. Verify retrieval using a newly created equal key.
3. Deliberately return a constant hash code. Confirm that correctness still works but collisions can reduce performance.

## 11. Quick revision

- equals defines logical equality.
- hashCode supports bucket selection.
- Equal objects must have equal hashes.
- Equal hashes do not imply equal objects.
- Never mutate a map key's equality fields after insertion.
