# Java Collections

For each collection, prepare four things: **internal structure, operation complexity, ordering/null behavior, and thread-safety**. Interviewers often ask you to choose a collection for a scenario and explain the trade-off.

## 1. Collection hierarchy and selection

1. [Collection hierarchy and Map implementations](collections/collection-hierarchy-map-variants.md)
2. [List, ArrayList and LinkedList](collections/list-arraylist-linkedlist.md)
3. [HashMap internals](collections/hashmap-internals.md)
4. [HashSet, TreeSet, TreeMap, Queue and Deque](collections/hashset-treemap-queue.md)
5. [Iterators, Comparable and Comparator](collections/iterators-comparable-comparator.md)
6. [Concurrent collections](collections/concurrent-collections.md)

## 2. Core concepts to know

- Collection is the root for List, Set and Queue; Map is a separate hierarchy.
- Hash-based collections use hashCode and equals.
- Sorted collections use Comparable or Comparator.
- ArrayList uses a resizable array; LinkedList uses doubly linked nodes.
- HashMap uses buckets and collision bins; know put, get, resize and treeification at a high level.
- Queue and Deque are interfaces; PriorityQueue is priority-based, not FIFO.
- Fail-fast iteration is not thread safety.
- ConcurrentHashMap provides atomic per-key operations; it does not make a multi-key workflow transactional.
- Understand unmodifiable views versus snapshots and shallow versus deep immutability.

## 3. Scenario-based questions

| Requirement | Likely choice | Reason |
|---|---|---|
| Fast lookup by key | HashMap | Expected O(1) basic operations |
| Preserve insertion order | LinkedHashMap / LinkedHashSet | Predictable iteration |
| Sorted keys or range search | TreeMap / TreeSet | Navigable sorted order |
| Unique values | Set | Uniqueness contract |
| Frequent indexed reads | ArrayList | O(1) index access |
| Stack or double-ended queue | ArrayDeque | Efficient end operations |
| Priority-based next item | PriorityQueue | Heap ordering |
| Concurrent key/value lookup | ConcurrentHashMap | Concurrent per-key operations |
| Bounded producer-consumer | BlockingQueue | Backpressure via capacity |

## 4. Cross-topic dependencies

Revise these Core Java lessons because collection behavior depends on them:
- [equals() and hashCode()](core-java/equals-hashcode.md)
- [Comparable and Comparator](collections/iterators-comparable-comparator.md)
- [Immutable collections and iteration](core-java/collections-immutability-iteration.md)
- [Generics and wildcards](core-java/generics.md)

## 5. Interview preparation checklist

Before calling Collections complete, make sure you can:
- Explain how HashMap calculates a bucket and resolves collisions.
- Explain load factor, capacity, size and resizing.
- Compare HashMap, LinkedHashMap, TreeMap and ConcurrentHashMap.
- Explain why mutable keys are dangerous.
- Compare ArrayList and LinkedList using operation costs.
- Explain HashSet uniqueness and TreeSet comparator behavior.
- Choose Queue, Deque, PriorityQueue or BlockingQueue for a scenario.
- Explain fail-fast, weakly consistent and snapshot iterators.
- Explain how ConcurrentHashMap methods avoid common get-then-put races.
- Discuss null support and iteration order for common implementations.

## 6. Practice

1. Count words with HashMap.merge.
2. Group values with computeIfAbsent.
3. Remove duplicates while preserving insertion order.
4. Find the k largest numbers using PriorityQueue.
5. Build a bounded producer-consumer example with ArrayBlockingQueue.
6. Sort employees by two fields using Comparator.thenComparing.
