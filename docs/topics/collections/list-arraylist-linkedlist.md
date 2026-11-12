# List, ArrayList and LinkedList

## 1. List contract

List is an ordered collection. It preserves positional order, allows duplicates, and provides index-based access. Common implementations include ArrayList, LinkedList and CopyOnWriteArrayList.

List is an interface; choose an implementation based on the access pattern rather than declaring every variable as ArrayList.

## 2. ArrayList internals

ArrayList uses a resizable array (an Object array internally). It tracks the number of elements separately from backing-array capacity.

- `get(index)` and `set(index)`: O(1).
- Append: amortized O(1); occasional resize copies the backing array.
- Insert/remove at index: O(n) due to shifting elements.
- Search by value: O(n).
- Memory: contiguous reference array, generally good cache locality.

```java
List<String> names = new ArrayList<>();
names.add("Asha");
names.add("Ravi");
names.add(1, "Mina");
System.out.println(names.get(1)); // Mina
```

Capacity growth policy is an implementation detail. Do not depend on an exact growth factor.

## 3. LinkedList internals

LinkedList is a doubly linked list. Each node stores an element and links to previous/next nodes.

- Add/remove at head or tail: O(1).
- Access by index: O(n), because nodes must be traversed.
- Insert/remove at a known ListIterator position: O(1) relinking.
- Search or find a position by value/index: O(n).
- Memory: extra node and link overhead; weaker cache locality.

LinkedList implements both List and Deque. ArrayDeque is usually a better default for queue/stack operations because it avoids one node allocation per element.

## 4. Comparison

| Operation | ArrayList | LinkedList |
|---|---:|---:|
| get(index) | O(1) | O(n) |
| Append | Amortized O(1) | O(1) |
| Add/remove first | O(n) | O(1) |
| Insert/remove middle by index | O(n) | O(n) to find; O(1) relink after location |
| Search by value | O(n) | O(n) |
| Memory/locality | Lower overhead, good locality | Higher overhead, pointer traversal |

LinkedList is not automatically faster for insertion: locating the insertion point may cost O(n).

## 5. ArrayList capacity and memory

An ArrayList has a logical size and a backing array capacity. Removing elements reduces size but may not shrink the backing array automatically. `trimToSize()` can reduce capacity to current size, but use it only when appropriate because later additions may trigger another resize.

If the expected element count is known, `new ArrayList<>(expectedCount)` can reduce resizing. It sets initial capacity, not list size; the list is still empty.

## 6. Iteration and modification

Structural modification outside a fail-fast iterator may cause ConcurrentModificationException. Use iterator.remove for supported removal during traversal, or `removeIf` for predicate-based removal.

```java
names.removeIf(String::isBlank);
```

Fail-fast is best-effort bug detection, not a thread-safety guarantee.

## 7. Choosing an implementation

- Frequent index reads and appends: ArrayList.
- Queue/stack operations at both ends: ArrayDeque.
- Frequent traversal and rare writes across threads: CopyOnWriteArrayList may fit.
- LinkedList: consider only when its end operations or iterator-position edits match the workload and measurements justify it.

For frequent membership checks, consider a Set. For concurrent access, choose a concurrent collection or explicit synchronization.

## 8. Common mistakes

- Choosing LinkedList because insertion is assumed to always be O(1).
- Confusing ArrayList capacity with size.
- Assuming ArrayList is thread-safe.
- Removing directly from a list inside an enhanced for loop.
- Using a List when uniqueness is the real requirement.
- Using LinkedList as the default queue instead of considering ArrayDeque or BlockingQueue.

## 9. Interview questions

**Q1. ArrayList vs LinkedList?**  
ArrayList has fast indexed access and better locality. LinkedList has efficient end operations but slow indexed access and greater memory overhead.

**Q2. Why is ArrayList append amortized O(1)?**  
Most appends are constant time; occasional array growth copies elements, and that cost is spread across many appends.

**Q3. Does new ArrayList<>(100) create 100 elements?**  
No. It creates capacity for about 100 elements; size remains zero.

**Q4. Is LinkedList insertion always O(1)?**  
No. Finding the insertion position may take O(n). Relinking is O(1) once the node position is known.

**Q5. Is ArrayList thread-safe?**  
No. Use external synchronization or an appropriate concurrent collection.

## 10. Practice

1. Compare indexed reads, append and remove-first operations for ArrayList and LinkedList.
2. Demonstrate the difference between ArrayList size and capacity.
3. Remove blank strings using removeIf and Iterator.remove.
4. Explain which collection you would use for an in-memory job queue and why.
