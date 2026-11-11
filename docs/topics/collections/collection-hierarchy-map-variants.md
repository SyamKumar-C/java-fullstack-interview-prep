# Java Collection Hierarchy and Map Implementations

> Priority: P1 | Level: Intermediate | Study time: 40 minutes

## 1. Collection hierarchy
- Collection: root for groups of elements.
- List: ordered sequence; duplicates allowed.
- Set: no duplicates according to equality/comparison rules.
- Queue: elements waiting for processing.
- Deque: double-ended queue.
- Map: key-value mapping; Map is separate from Collection.

```text
Iterable
  └── Collection
      ├── List
      ├── Set
      │   └── SortedSet / NavigableSet
      └── Queue
          └── Deque

Map (separate hierarchy)
  ├── SortedMap / NavigableMap
  └── ConcurrentMap
```

## 2. Map implementations

| Type | Ordering | Typical use |
|---|---|---|
| HashMap | No iteration-order guarantee | General lookup |
| LinkedHashMap | Insertion order by default; optional access order | Predictable iteration |
| TreeMap | Sorted by key | Range and sorted-key operations |
| ConcurrentHashMap | No general order guarantee | Concurrent access |
| Hashtable | Legacy synchronized map | Usually prefer modern alternatives |

## 3. LinkedHashMap access order

```java
Map<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);
map.put(1, "A");
map.put(2, "B");
map.put(3, "C");
map.get(1);
System.out.println(map.keySet()); // [2, 3, 1]
```

The third constructor argument enables access order. This can be a building block for an LRU cache, but a production cache also needs expiry, size, and concurrency policies.

## 4. TreeMap
TreeMap sorts keys using natural ordering or a Comparator. Basic get/put/remove operations are O(log n).

```java
Map<String, Integer> scores = new TreeMap<>();
scores.put("Maya", 90);
scores.put("Arun", 85);
System.out.println(scores); // {Arun=85, Maya=90}
```

## 5. Choose by requirement
- Index access: List.
- Uniqueness without order: HashSet.
- Insertion order: LinkedHashSet/LinkedHashMap.
- Sorted/range operations: TreeSet/TreeMap.
- Producer-consumer: Queue/BlockingQueue.
- Concurrent key-value access: ConcurrentHashMap.

Hash collections depend on equals/hashCode. Tree collections use comparison; a comparator returning zero treats keys as equivalent for map/set uniqueness. Null support varies: HashMap allows a null key; ConcurrentHashMap does not.

## 6. Common mistakes
- Assuming HashMap preserves insertion order.
- Assuming Map extends Collection.
- Mutating a key after inserting it into HashMap.
- Using Comparator inconsistent with equals without understanding uniqueness.
- Assuming ConcurrentHashMap makes mutable values thread-safe.

## 7. Interview questions
**Q1. Why is Map separate from Collection?** Map stores key-value associations, not individual elements.

**Q2. HashMap vs LinkedHashMap?** LinkedHashMap maintains predictable iteration order.

**Q3. HashMap vs TreeMap?** Expected constant-time basic operations versus sorted keys with logarithmic operations.

**Q4. Can ConcurrentHashMap contain null?** No, it disallows null keys and values.

**Q5. Can TreeMap use custom order?** Yes, pass a Comparator.

## 8. Practice
1. Count word frequency with HashMap.
2. Preserve insertion order with LinkedHashMap.
3. Create a sorted report with TreeMap.
4. Explain why a mutable key can become unreachable.
