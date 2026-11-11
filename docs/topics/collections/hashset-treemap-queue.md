# HashSet, TreeMap, Queue and Deque

## 1. HashSet

HashSet stores unique elements. It is backed by a HashMap; each set element is stored as a map key. Equality and hashCode determine duplicates.

```java
Set<String> roles = new HashSet<>();
roles.add("ADMIN");
roles.add("USER");
roles.add("ADMIN");
System.out.println(roles.size()); // 2
```

HashSet does not guarantee iteration order. Use LinkedHashSet for insertion order and TreeSet for sorted order.

## 2. TreeMap

TreeMap stores key/value pairs sorted by key. It uses natural ordering or a Comparator and is based on a red-black tree. Basic get/put/remove operations are O(log n).

```java
Map<Integer, String> employees = new TreeMap<>();
employees.put(30, "Ravi");
employees.put(10, "Asha");
employees.put(20, "Mina");
System.out.println(employees); // keys in sorted order
```

TreeMap is useful for range queries such as `subMap`, `headMap` and `tailMap`.

## 3. Queue

Queue generally follows FIFO: first in, first out. Its method pairs differ in how they handle failure.

| Operation | Throws on failure | Special value |
|---|---|---|
| Insert | `add` | `offer` returns false |
| Remove head | `remove` | `poll` returns null |
| Inspect head | `element` | `peek` returns null |

PriorityQueue is not FIFO; it returns the least element according to ordering by default.

## 4. Deque

Deque supports insertion/removal at both ends. ArrayDeque is a common stack/queue implementation and is usually preferred over legacy Stack.

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
stack.push(20);
System.out.println(stack.pop()); // 20

Deque<Integer> queue = new ArrayDeque<>();
queue.offerLast(10);
queue.offerLast(20);
System.out.println(queue.pollFirst()); // 10
```

## 5. Choosing the right structure

- Unique unordered values: HashSet.
- Unique values in insertion order: LinkedHashSet.
- Unique sorted values: TreeSet.
- Sorted key/value and range lookup: TreeMap.
- FIFO: ArrayDeque or a Queue implementation.
- Priority-based retrieval: PriorityQueue.
- Stack: ArrayDeque.

## 6. Complexity overview

| Structure | Typical operation |
|---|---|
| HashSet | add/contains/remove average O(1) |
| TreeMap | get/put/remove O(log n) |
| ArrayDeque | add/remove at ends amortized O(1) |
| PriorityQueue | peek O(1), offer/poll O(log n) |

Hash-based complexity can degrade with poor hash distribution. Complexity also depends on the operation and implementation.

## 7. Common mistakes

- Expecting HashSet iteration order.
- Mutating an object in a way that changes its hashCode while it is in a HashSet.
- Assuming PriorityQueue iteration is fully sorted; only the head is guaranteed to be highest/lowest priority.
- Using Stack instead of ArrayDeque for new code.
- Providing a Comparator inconsistent with equals without understanding the effect on sorted maps/sets.

## 8. Interview questions

**Q1. How does HashSet prevent duplicates?** It uses hashCode to locate a bucket and equals to identify an existing equal element.

**Q2. HashSet vs TreeSet?** HashSet is hash-based and unordered; TreeSet is sorted and generally O(log n).

**Q3. Queue vs Deque?** Queue focuses on head/tail operations; Deque supports both ends.

**Q4. Is PriorityQueue FIFO?** No. It orders elements by priority, not insertion time.

## 9. Practice

Remove duplicates from a list while preserving insertion order. Then use TreeMap to find all employees whose IDs fall within a given range.
