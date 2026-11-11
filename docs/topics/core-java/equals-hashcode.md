# equals() and hashCode()

## 1. The contract

Every Java object inherits `equals()` and `hashCode()` from `Object`.

If two objects are equal according to `equals()`, they must return the same hash code. Unequal objects may still have the same hash code (a collision).

A correct `equals()` implementation should be:
- Reflexive: `x.equals(x)` is true.
- Symmetric: if `x.equals(y)`, then `y.equals(x)`.
- Transitive: if x equals y and y equals z, x equals z.
- Consistent while relevant state is unchanged.
- False for null.

## 2. Example

```java
import java.util.Objects;

class Employee {
    private final int id;
    private final String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Employee other)) return false;
        return id == other.id && Objects.equals(name, other.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}
```

For older Java versions, replace pattern matching in `instanceof` with an explicit cast.

## 3. Why HashMap needs both

Hash-based collections use the hash code to find a bucket, then use equality to identify the matching key within that bucket.

If you override `equals()` but not `hashCode()`, two logically equal keys may go to different buckets. A lookup using an equal but different object can fail.

## 4. Mutable keys are dangerous

Do not change fields used by `equals()` or `hashCode()` after inserting an object as a HashMap key. The object's new hash may point to a different bucket, while the entry remains in the old bucket.

Prefer immutable key classes.

## 5. Interview questions

**Can two unequal objects have the same hash code?** Yes. That is a hash collision.

**Can equal objects have different hash codes?** No. That violates the contract.

**Does HashMap use only hashCode()?** No. It uses hash information to locate a bucket and equality checks to find the key.

**What if hashCode always returns 1?** Correctness can remain, but performance may degrade because many keys collide.

**Should we use all fields in equals?** Use the fields that define logical identity, not necessarily every field.

## 6. Practice

Create two Employee objects with the same ID and name. Put one in a HashMap and retrieve the value using the other. Then remove `hashCode()` and observe the failure.
