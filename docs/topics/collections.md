# Java Collections

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 2–3 hours

The Collections Framework provides standard interfaces and implementations for storing and processing groups of objects.

## 1. Collection hierarchy

- **List:** Ordered collection; duplicates are allowed. Examples: ArrayList, LinkedList.
- **Set:** Unique elements. Examples: HashSet, LinkedHashSet, TreeSet.
- **Queue / Deque:** Elements are processed according to queue or double-ended queue rules.
- **Map:** Key-value pairs with unique keys. Map is part of the framework but does not extend Collection.

## 2. ArrayList vs LinkedList

| Point | ArrayList | LinkedList |
|---|---|---|
| Structure | Resizable array | Doubly linked list |
| Indexed read | O(1) | O(n) |
| Add at end | Amortized O(1) | O(1) |
| Insert/remove in middle | O(n) due to shifting/search | O(n) to reach position, O(1) relink |
| Memory | Usually lower | Extra node pointers |

Use ArrayList for most general-purpose list use cases. Choose LinkedList only when its deque behavior or known insertion/removal pattern justifies it.

## 3. Set implementations

- **HashSet:** Unordered, hash-based uniqueness; average O(1) add, remove, contains.
- **LinkedHashSet:** Maintains insertion order.
- **TreeSet:** Maintains sorted order; operations typically O(log n).

A Set decides whether values are duplicates using equality and hashing (for hash-based sets) or ordering comparison (for TreeSet).

## 4. Map implementations

- **HashMap:** Hash-based key-value storage; permits one null key and null values.
- **LinkedHashMap:** Predictable iteration order; can support access-order maps for LRU-style use.
- **TreeMap:** Sorted keys; O(log n) core operations.
- **ConcurrentHashMap:** Thread-safe concurrent access; does not allow null keys or values.

### How HashMap works

1. It calculates a hash from the key.
2. The hash helps select a bucket.
3. If multiple keys land in one bucket, collisions are handled within that bucket.
4. It uses hash and equals to find the exact key.
5. When the map grows beyond its resize threshold, its table is expanded and entries are redistributed.

Good hash distribution improves performance. A correct key should have stable equals and hashCode values while it is stored in the map.

## 5. Comparable vs Comparator

**Comparable** defines a class's natural ordering through compareTo(). **Comparator** defines an external or alternative ordering through compare().

```java
record Employee(int id, String name) {}

Comparator<Employee> byName =
    Comparator.comparing(Employee::name);

Comparator<Employee> byIdDescending =
    Comparator.comparingInt(Employee::id).reversed();

List<Employee> employees = new ArrayList<>();
employees.sort(byName);
```

Use Comparator when you need multiple sort orders without changing the class.

## 6. Iterators and modification

- Iterator supports traversal and optional removal through iterator.remove().
- ListIterator supports forward/backward traversal and list-specific updates.
- Many standard collection iterators are fail-fast: structural modification outside the iterator may cause ConcurrentModificationException. This is a best-effort bug-detection behavior, not a concurrency guarantee.

## 7. Interview questions

??? question "Why must equals() and hashCode() be consistent?"
    If two objects are equal according to equals(), they must return the same hashCode(). Hash-based collections use the hash to find a bucket and equals() to identify the matching key. Violating the contract can make lookups fail.

??? question "What happens when two HashMap keys have the same hash?"
    They collide into the same bucket. HashMap checks keys within that bucket using hash information and equals(). A collision does not mean the keys are equal.

??? question "Does creating an index or using HashMap guarantee O(1) in every case?"
    No. HashMap operations are average O(1), but collisions and resizing affect actual work. Similarly, a database may choose not to use an index based on its cost estimate.

??? question "When would you use ConcurrentHashMap?"
    Use it when multiple threads need safe concurrent access to a shared map. It provides concurrency support without locking the entire map for every operation.

## 8. Common mistakes

- Mutating a key's fields used by equals/hashCode after inserting it into a HashMap.
- Assuming HashMap iteration order is stable.
- Using TreeSet when ordering/equality semantics are not understood.
- Modifying a collection during enhanced for-loop iteration instead of using the iterator or a safe concurrent design.

## 9. Practice

1. Count word frequency with HashMap.
2. Remove duplicates while preserving insertion order.
3. Sort employees by department, then salary descending.
4. Explain the difference between HashMap and ConcurrentHashMap.
