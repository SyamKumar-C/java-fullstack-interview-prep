# List, ArrayList and LinkedList

## 1. List contract

List is an ordered collection. It allows duplicates and supports positional access. Common implementations include ArrayList, LinkedList and CopyOnWriteArrayList.

## 2. ArrayList

ArrayList uses a resizable array. It provides fast index access, typically O(1), and amortized O(1) append. Inserting or removing near the beginning or middle shifts later elements and is O(n).

```java
List<String> names = new ArrayList<>();
names.add("Asha");
names.add("Ravi");
names.add(1, "Mina");
System.out.println(names.get(1)); // Mina
```

Capacity can grow automatically. The default growth strategy is an implementation detail; do not write code that depends on an exact capacity increase.

## 3. LinkedList

LinkedList is a doubly linked list. Adding/removing at either end is O(1) when the position is already known. Access by index is O(n), because it must traverse nodes.

It also implements Deque, so it can be used as a queue or deque. ArrayDeque is often a better choice for stack/queue use because it avoids per-node allocation.

## 4. Comparison

| Feature | ArrayList | LinkedList |
|---|---|---|
| Internal structure | Resizable array | Doubly linked nodes |
| get(index) | O(1) | O(n) |
| Append | Amortized O(1) | O(1) |
| Insert/remove middle | O(n) shifting | O(n) to find position, then O(1) relink |
| Memory overhead | Lower | Higher per node |
| Cache locality | Better | Worse |

LinkedList is not automatically faster for insertion. Finding the insertion point may dominate the cost.

## 5. Iteration and modification

Structural modification while using a fail-fast iterator may cause ConcurrentModificationException. Use the iterator's own `remove()` method when removing during iteration.

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    if (it.next().isBlank()) {
        it.remove();
    }
}
```

Fail-fast behavior is best-effort bug detection, not a thread-safety guarantee.

## 6. Practical scenarios

- API response list with frequent indexed reads: ArrayList.
- Queue/stack operations: ArrayDeque.
- Frequent insertion in the middle after locating a node: consider LinkedList only after measuring.
- Concurrent read-heavy list: CopyOnWriteArrayList may fit if writes are rare.

## 7. Common mistakes

- Choosing LinkedList because insertion is assumed to be O(1) in every case.
- Using a list for frequent membership checks when a Set is more suitable.
- Modifying a list structurally inside an enhanced for-loop.
- Assuming ArrayList is thread-safe.

## 8. Interview questions

**Q1. ArrayList vs LinkedList?** ArrayList offers fast indexed access and better locality; LinkedList has efficient end operations but slow indexed access and more memory overhead.

**Q2. Why is ArrayList append amortized O(1)?** Most appends are constant time; occasional resizing copies the backing array, and that cost is spread across many appends.

**Q3. Is ArrayList thread-safe?** No. Use external synchronization or an appropriate concurrent collection.

**Q4. What is fail-fast?** Iterators may detect structural modification outside the iterator and throw ConcurrentModificationException.

## 9. Practice

Write a benchmark-style comparison for repeated get(index), append and remove-first operations. Explain why the data size and access pattern matter.
