# HashMap Internals in Java

## 1. What is HashMap?

`HashMap<K,V>` stores key-value mappings and provides average constant-time lookup and insertion when hashes are well distributed. It does not guarantee iteration order and is not thread-safe.

Use it for fast lookup by a key, such as employeeId to Employee or productCode to stock details.

## 2. Internal data structure

A HashMap maintains an array of buckets. A bucket can be empty or contain one or more entries. In modern Java implementations, a heavily collided bucket can be represented as a balanced tree after certain thresholds are met.

An entry conceptually contains:
- hash
- key
- value
- next entry reference (for a linked bin)

Implementation details can vary by Java version; the bucket and collision model is the important interview concept.

## 3. How put() works

Simplified flow:

1. If the table is not initialized, allocate it.
2. Compute the key hash (null key is handled specially).
3. Spread high hash bits so more bits can affect bucket selection.
4. Calculate the bucket index using the table capacity.
5. If the bucket is empty, insert a node.
6. If occupied, compare hashes and then keys using identity or `equals()`.
7. If the key already exists, replace its value.
8. Otherwise add a new node to the bin (or tree structure).
9. Increase size and resize if the threshold is exceeded.

Simplified illustration (not the exact JDK source):

```java
int hash = key == null ? 0 : spread(key.hashCode());
int index = (table.length - 1) & hash;
```

The table capacity is kept as a power of two in common OpenJDK implementations, making this index calculation efficient.

## 4. How get() works

1. Calculate the requested key's hash.
2. Find the bucket index.
3. Check the first node.
4. Compare hash and key equality.
5. Search the remaining list or tree if needed.
6. Return the value, or null if no mapping exists.

Because null values are allowed, use `containsKey(key)` when you must distinguish an absent key from a key mapped to null.

## 5. hashCode() and equals()

The hash identifies a likely bucket; equals determines whether a key is logically the same as an existing key.

Contract:
- If `a.equals(b)` is true, `a.hashCode()` must equal `b.hashCode()`.
- Equal hash codes do not require equal objects.
- Equality fields of a key should not change while it is stored in the map.

A broken contract can make a logically equal key impossible to find.

## 6. Collision handling and treeification

A collision occurs when different keys map to the same bucket. HashMap handles collisions by storing multiple entries in that bucket.

In OpenJDK 8 and later, bins can be converted from linked nodes to tree nodes when the bin is sufficiently large and the table is large enough. Commonly discussed implementation thresholds are treeify threshold 8, minimum treeify capacity 64, and untreeify threshold 6. Treat these as implementation details, not a permanent API guarantee.

Tree bins improve worst-case lookup behavior for suitable keys, but good hash distribution is still important.

## 7. Capacity, load factor and resizing

- Capacity: number of buckets in the table.
- Load factor: how full the table may become before resizing.
- Threshold: capacity multiplied by load factor.

Default initial capacity is commonly 16 and default load factor 0.75. When size exceeds threshold, the table grows (commonly doubles), and entries are redistributed. Resizing costs time, so if expected size is known, pre-sizing can reduce repeated resizing.

```java
Map<Long, Employee> byId = new HashMap<>(expectedSize);
```

Be aware that constructor capacity is not always the exact number of expected mappings because the load factor affects the threshold.

## 8. Complexity

| Operation | Typical average | Collision-heavy case |
|---|---:|---:|
| get | O(1) | Can degrade; tree bins improve suitable cases |
| put | O(1) amortized | Can degrade due to collisions/resizing |
| remove | O(1) average | Depends on bin structure |

These are expected complexity descriptions, not hard real-time guarantees.

## 9. Important behavior

- One null key is supported.
- Multiple null values are supported.
- No iteration-order guarantee.
- Not synchronized; concurrent structural modification is unsafe.
- Iterators are fail-fast on a best-effort basis, not a thread-safety mechanism.
- Use ConcurrentHashMap for concurrent access when its semantics fit.

## 10. Practical example

```java
Map<String, Integer> counts = new HashMap<>();
counts.merge("java", 1, Integer::sum);
counts.merge("java", 1, Integer::sum);
counts.merge("spring", 1, Integer::sum);
System.out.println(counts); // values: java=2, spring=1
```

`merge` is useful for counters because it inserts the first value and combines later values.

## 11. Interview questions and answers

**Q1. How does HashMap work internally?**  
It hashes the key, maps the hash to a bucket, then checks entries in that bucket using hash and equals. Collisions are stored in a bin, which may become a tree in modern implementations.

**Q2. Why override hashCode when overriding equals?**  
Equal keys must reach the same bucket. Otherwise a map may not compare them and a lookup using an equal key can fail.

**Q3. What is the load factor?**  
It controls the occupancy threshold that triggers resizing. A lower value uses more memory but can reduce collisions; a higher value saves table space but may increase collisions.

**Q4. What happens when two keys have the same hash?**  
They collide. HashMap stores both in the same bucket and distinguishes them using equality.

**Q5. Is HashMap thread-safe?**  
No. Use ConcurrentHashMap for concurrent access, or external synchronization if a whole-map atomic operation is required.

**Q6. Why is a mutable key dangerous?**  
Changing a field used by hashCode can change the bucket calculation after insertion. The entry may remain in its old bucket and become difficult to retrieve.

**Q7. Does HashMap preserve insertion order?**  
No. Use LinkedHashMap when insertion/access order matters, or TreeMap for sorted key order.

**Q8. What is the difference between capacity and size?**  
Capacity is the bucket-array length; size is the number of mappings currently stored.

## 12. Coding exercises

1. Count word frequencies with `merge`.
2. Group employees by department using `computeIfAbsent`.
3. Use a custom immutable key with two fields and verify lookup using a separate equal instance.
4. Find the first non-repeating character using a LinkedHashMap.

## 13. Quick revision

HashMap = bucket array + collision bins. hashCode selects the bucket; equals identifies the key. Average get/put is O(1), resizing happens beyond threshold, and HashMap is not thread-safe.
