# HashMap Internals in Java

## 1. What HashMap provides

`HashMap<K,V>` stores key-value mappings. It gives expected O(1) get/put/remove when hashes are well distributed. It does not guarantee iteration order and is not thread-safe.

Typical use: map an employee ID to an Employee, count words, or look up configuration by key.

## 2. Internal structure

A HashMap uses a table (array) of buckets. Each bucket may be empty or contain:
- One node.
- A linked chain of nodes after collisions.
- A balanced red-black tree for a heavily collided bin in modern OpenJDK implementations.

A node conceptually stores hash, key, value and a next reference. This is an implementation description, not a public API guarantee.

## 3. How hash calculation and bucket index work

HashMap calls the key's `hashCode()`. In OpenJDK implementations, high bits are spread into lower bits, because the bucket index uses a mask and otherwise some high-bit differences would not help select a bucket.

Simplified illustration (not exact JDK source):

```java
int h = key == null ? 0 : key.hashCode();
int hash = h ^ (h >>> 16);
int index = (table.length - 1) & hash;
```

The table capacity is maintained as a power of two in common OpenJDK implementations. The bit mask is then an efficient way to calculate the bucket index.

A null key is handled with hash zero. HashMap permits one null key and multiple null values.

## 4. How put(key, value) works

Simplified sequence:

1. Initialize the table if required.
2. Calculate the key hash and bucket index.
3. If the bucket is empty, insert a node.
4. If the first node matches by hash and key equality, replace its value.
5. Otherwise search the list/tree in the bucket.
6. If an equal key exists, replace its value.
7. If not, add a new node to the bin.
8. Increase mapping count; resize when the threshold is crossed.

The key match is based on hash plus identity/equality. A good `equals()` and `hashCode()` contract is essential.

## 5. How get(key) works

1. Calculate the key hash and bucket index.
2. Check the first node.
3. Compare hash and key identity/equality.
4. Search the rest of the linked bin or tree if needed.
5. Return the value, or null if no mapping exists.

Because null values are allowed, a null result alone cannot tell whether a key is absent or mapped to null. Use `containsKey(key)` when that distinction matters.

## 6. Collision handling and tree bins

A collision means multiple keys map to the same bucket. HashMap keeps them in the same bin and distinguishes them using key equality.

OpenJDK 8+ can convert a long linked bin into a red-black tree when thresholds and table-size conditions are met. Common implementation constants are:
- Treeify threshold: 8 nodes.
- Minimum table capacity for treeification: 64.
- Untreeify threshold: 6 nodes.

These are implementation details, not contractual guarantees. When the table is too small, resizing may be preferred over treeification. Tree bins improve collision-heavy lookup behavior, but do not make poor hash functions desirable.

## 7. Capacity, load factor, threshold and resize

- **Size:** number of mappings currently stored.
- **Capacity:** number of buckets in the table.
- **Load factor:** occupancy ratio used to calculate the resize threshold.
- **Threshold:** commonly capacity multiplied by load factor.

Default initial capacity is 16 and default load factor is 0.75 in standard HashMap constructors. When size exceeds threshold, the table grows, commonly doubling its capacity.

Resizing redistributes nodes into the new table. In OpenJDK 8+, when capacity doubles, a node from an old bucket generally either stays at the same index or moves by the old capacity, based on one hash bit. This avoids recomputing the full hash.

If expected mappings are known, pre-size to reduce repeated resizing. Constructor capacity is not necessarily the exact mapping count you can store before resize because load factor affects threshold.

## 8. Complexity and performance

| Operation | Expected case | Notes |
|---|---:|---|
| get | O(1) | Depends on hash distribution |
| put | O(1) amortized | Resize can make an individual operation expensive |
| remove | O(1) average | Depends on bin shape |
| iteration | O(capacity + size) | Scans table and entries |

Collision-heavy behavior can degrade. Tree bins improve suitable collision cases, but complexity is not a real-time guarantee.

## 9. equals() and hashCode() contract

- If `a.equals(b)` is true, both must return the same hash code.
- Equal hash codes do not mean objects are equal.
- Do not mutate fields used by equality/hash calculation while a key is stored in the map.

If a key's hash changes after insertion, lookup may search a different bucket from the one holding the entry.

## 10. Important API methods

- `putIfAbsent(k, v)`: add only when no non-null mapping is present.
- `getOrDefault(k, defaultValue)`: return a default if no mapping exists.
- `computeIfAbsent(k, fn)`: calculate and store a value when absent/null.
- `computeIfPresent(k, fn)`: recalculate an existing non-null mapping.
- `compute(k, fn)`: recalculate based on current mapping; returning null removes the mapping.
- `merge(k, value, fn)`: insert value if absent/null; otherwise combine.
- `containsKey(k)`: distinguish missing key from key mapped to null.

```java
Map<String, Long> counts = new HashMap<>();
counts.merge("java", 1L, Long::sum);
counts.merge("java", 1L, Long::sum);
counts.merge("spring", 1L, Long::sum);
// java -> 2, spring -> 1
```

These methods make single-threaded update logic concise. HashMap itself is still not safe for unsynchronized concurrent updates.

## 11. HashMap vs related maps

| Map | Key ordering | Null support | Typical use |
|---|---|---|---|
| HashMap | No guarantee | One null key; null values | General lookup |
| LinkedHashMap | Insertion or access order | Allows null | Predictable iteration, LRU building block |
| TreeMap | Sorted | Null key generally not allowed with natural ordering | Range/sorted-key operations |
| ConcurrentHashMap | No order guarantee | No null keys/values | Concurrent per-key operations |
| Hashtable | No order guarantee | No null keys/values | Legacy synchronized map |

## 12. Thread safety

HashMap is not synchronized. Concurrent reads are safe only when the map is safely published and not concurrently modified; concurrent writes or read/write races require coordination.

Use ConcurrentHashMap for concurrent key-value access when its semantics fit. It provides atomic per-key operations, not a transaction across arbitrary multiple keys.

## 13. Common interview traps

- Capacity is not the same as size.
- HashMap does not preserve insertion order.
- Same hash does not mean same key.
- `equals()` alone is not enough; `hashCode()` must agree.
- A null get result may mean absent or mapped-to-null.
- Fail-fast iterators are not a thread-safety mechanism.
- Treeification thresholds are JDK implementation details.
- A thread-safe map does not make its mutable values thread-safe.

## 14. Interview questions

**Q1. Explain HashMap put and get.**  
Hash the key, select a bucket, then compare entries in that bin using hash and equals. Put replaces an existing equal key or adds a node; get returns its value.

**Q2. Why is capacity a power of two?**  
It allows efficient bucket indexing with a bit mask and supports the implementation's resize strategy.

**Q3. What is the load factor?**  
It determines the threshold at which the table resizes. Lower load can reduce collisions but uses more memory.

**Q4. What happens on collision?**  
Entries share a bucket in a linked bin or, for suitable large bins in modern OpenJDK, a red-black tree.

**Q5. Why are mutable keys dangerous?**  
Changing a hash/equality field after insertion can make the entry unreachable through normal lookup.

**Q6. What is the difference between size and capacity?**  
Size counts mappings; capacity counts buckets.

**Q7. Is HashMap thread-safe?**  
No. Use ConcurrentHashMap or external synchronization for concurrent updates.

**Q8. Why can get() return null for a present key?**  
HashMap allows null values. Use containsKey to distinguish a present null mapping from an absent key.

## 15. Practice exercises

1. Count word frequencies with merge.
2. Group employees by department with computeIfAbsent.
3. Use a two-field immutable key and retrieve a value using a separately created equal key.
4. Show the failure caused by mutating a key after insertion.
5. Compare HashMap and LinkedHashMap iteration order.
6. Explain how resize changes bucket positions when capacity doubles.

## 16. Quick revision

HashMap = bucket array + collision bins. hashCode selects a bucket; equals identifies a key. Expected get/put is O(1). Capacity, size and threshold are different. HashMap allows null, does not preserve order, and is not thread-safe.
