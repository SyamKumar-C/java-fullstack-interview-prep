# Java Collection Hierarchy and Map Implementations

## 1. The hierarchy

`Iterable` provides the ability to obtain an Iterator. `Collection` extends Iterable and is the main root for groups of individual elements.

```text
Iterable
  └── Collection
      ├── List
      ├── Set
      │   └── SortedSet
      │       └── NavigableSet
      └── Queue
          └── Deque

Map (separate hierarchy)
  ├── SortedMap
  │   └── NavigableMap
  └── ConcurrentMap
```

Map is not a subtype of Collection because it stores key-value mappings rather than individual elements.

## 2. Core interfaces

| Interface | Main contract |
|---|---|
| List | Ordered sequence, duplicates allowed, index access |
| Set | No duplicate elements according to equality/order |
| Queue | Elements waiting for retrieval/processing |
| Deque | Insert/remove at both ends |
| Map | Key-value mappings; keys are unique |
| SortedSet/SortedMap | Elements/keys kept in sorted order |
| NavigableSet/NavigableMap | Neighbour/range navigation methods |
| BlockingQueue | Blocking producer-consumer operations |
| ConcurrentMap | Concurrent map operations |

## 3. Map implementations

| Implementation | Ordering | Thread safety | Typical purpose |
|---|---|---|---|
| HashMap | No guarantee | No | General lookup |
| LinkedHashMap | Insertion or access order | No | Predictable iteration/LRU building block |
| TreeMap | Sorted by key | No | Range and sorted-key operations |
| ConcurrentHashMap | No guarantee | Concurrent operations | Concurrent in-memory lookup |
| Hashtable | No guarantee | Legacy synchronized | Usually avoid in new code |
| EnumMap | Enum key declaration order | No | Efficient map with enum keys |
| IdentityHashMap | Reference identity, not equals | No | Specialized identity-based maps |
| WeakHashMap | No iteration order guarantee | No | Entries can disappear when keys are no longer strongly referenced |

Do not choose specialized maps without understanding their unusual key and lifecycle semantics.

## 4. LinkedHashMap access order

```java
Map<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);
map.put(1, "A");
map.put(2, "B");
map.put(3, "C");
map.get(1);
System.out.println(map.keySet()); // [2, 3, 1]
```

The third constructor argument enables access order. LinkedHashMap can be used as a basic LRU building block by combining access order with removeEldestEntry, but a production cache also needs expiry, concurrency, size and metrics policies.

## 5. Sorted and navigable operations

TreeMap and TreeSet support methods such as:
- firstKey/lastKey or first/last.
- lower/floor/ceiling/higher.
- headMap/tailMap/subMap.
- descendingMap/descendingSet.

These are useful for range queries, nearest-key lookups and time/ID ordered data.

## 6. How to choose

- Need positional access and duplicates: List.
- Need uniqueness: Set.
- Need key/value lookup: Map.
- Need sorted keys or range queries: TreeMap.
- Need predictable insertion/access order: LinkedHashMap.
- Need concurrent per-key operations: ConcurrentHashMap.
- Need enum keys: EnumMap.
- Need producer-consumer: BlockingQueue.
- Need stack/queue at both ends: Deque.

Hash-based structures depend on equals/hashCode. Sorted structures depend on Comparable/Comparator. A comparator returning zero means equivalent ordering keys in TreeMap/TreeSet, even if equals returns false.

## 7. Null behavior reminders

Null support differs by implementation:
- HashMap: one null key and null values allowed.
- ConcurrentHashMap: no null keys or values.
- TreeMap: natural ordering does not accept a null key; a custom comparator may define null handling.
- ArrayDeque: no null elements.
- List.of/List.copyOf: no null elements.
- HashSet: one null element.

Always check the concrete implementation contract rather than assuming all collections treat null the same way.

## 8. Common interview mistakes

- Saying Map extends Collection.
- Assuming all Set implementations use hashCode/equals.
- Assuming all Queue implementations are FIFO.
- Assuming every collection allows null.
- Confusing sorted order with insertion order.
- Assuming concurrent collections coordinate across multiple JVMs.
- Ignoring comparator consistency with equals.

## 9. Interview questions

**Q1. Why is Map separate from Collection?**  
Map stores key-value pairs, not individual elements.

**Q2. HashMap vs LinkedHashMap?**  
LinkedHashMap maintains insertion or access iteration order; HashMap does not guarantee order.

**Q3. HashMap vs TreeMap?**  
HashMap is hash-based with expected O(1) basic operations; TreeMap sorts keys with O(log n) basic operations.

**Q4. What is NavigableMap?**  
A sorted map with navigation methods such as lowerEntry, floorEntry, ceilingEntry and higherEntry.

**Q5. When use EnumMap?**  
When keys are values of one enum type; it is compact and efficient.

**Q6. What makes IdentityHashMap different?**  
It compares keys by reference identity (==), not equals. It is for specialized cases, not a general replacement for HashMap.

**Q7. Can ConcurrentHashMap be shared across application instances?**  
No. It is local to one JVM.

## 10. Practice

1. Select a collection for a list of unique sorted customer IDs.
2. Implement an insertion-order report with LinkedHashMap.
3. Build a basic access-order map and explain its LRU behavior.
4. Compare EnumMap and HashMap for enum keys.
5. Explain why IdentityHashMap should not be used for ordinary business-key lookup.
