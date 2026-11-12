# HashSet, TreeSet, TreeMap, Queue and Deque

## 1. HashSet

HashSet stores unique elements and is backed by a HashMap. Elements are stored as map keys, so hashCode and equals determine duplicates.

```java
Set<String> roles = new HashSet<>();
roles.add("ADMIN");
roles.add("USER");
roles.add("ADMIN");
System.out.println(roles.size()); // 2
```

HashSet has no iteration-order guarantee. It permits one null element. Do not mutate equality/hash fields of an element while it is in the set.

## 2. LinkedHashSet and TreeSet

- LinkedHashSet preserves insertion order using linked iteration structure.
- TreeSet keeps elements sorted using natural ordering or a Comparator.
- HashSet generally offers expected O(1) add/contains/remove.
- TreeSet offers O(log n) add/contains/remove.

TreeSet treats two elements as the same set entry when comparison returns zero. If the comparator says two distinct objects compare as zero, the second is not added. Ideally, ordering should be consistent with equals.

## 3. TreeMap

TreeMap stores key/value mappings sorted by key. It uses a red-black tree and supports range operations.

```java
NavigableMap<Integer, String> employees = new TreeMap<>();
employees.put(30, "Ravi");
employees.put(10, "Asha");
employees.put(20, "Mina");

System.out.println(employees.firstKey()); // 10
System.out.println(employees.subMap(10, true, 30, false));
```

Basic get/put/remove operations are O(log n). TreeMap uses natural ordering or a Comparator. With natural ordering, null keys are not supported; comparator behavior determines whether null can be handled.

## 4. Queue method pairs

Queue generally represents elements waiting for processing. FIFO is common, but not every Queue is FIFO (PriorityQueue is priority ordered).

| Action | Throws on failure | Special return value |
|---|---|---|
| Insert | add(e) | offer(e) returns false |
| Remove head | remove() | poll() returns null |
| Inspect head | element() | peek() returns null |

Use offer/poll/peek when failure or emptiness is expected and should be represented by a return value.

## 5. PriorityQueue

PriorityQueue returns the least element according to natural ordering by default, or according to its Comparator. It is heap-backed.

- peek: O(1).
- offer/add: O(log n).
- poll/remove head: O(log n).
- contains/remove arbitrary element: O(n).

Iteration is not guaranteed to produce fully sorted order. Only the head is guaranteed to be the next element according to priority.

## 6. Deque and ArrayDeque

Deque supports insertion and removal at both ends. ArrayDeque is a resizable circular-array implementation and is a common choice for stack and queue use.

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

ArrayDeque does not permit null elements. Prefer it over legacy Stack for a typical single-threaded stack. It is not thread-safe.

## 7. Choosing a structure

| Requirement | Candidate |
|---|---|
| Unique values, no ordering requirement | HashSet |
| Unique values, insertion order | LinkedHashSet |
| Unique values, sorted | TreeSet |
| Sorted key/value and range lookup | TreeMap |
| FIFO queue in one thread | ArrayDeque |
| Stack in one thread | ArrayDeque |
| Priority-based retrieval | PriorityQueue |
| Blocking producer-consumer | BlockingQueue implementation |

## 8. Common mistakes

- Expecting HashSet iteration order.
- Mutating an element after inserting it into HashSet.
- Assuming PriorityQueue iteration is sorted.
- Assuming every Queue is FIFO.
- Using Stack instead of ArrayDeque for new single-threaded code.
- Ignoring comparator consistency with equals in TreeSet/TreeMap.
- Assuming ArrayDeque or PriorityQueue is thread-safe.

## 9. Interview questions

**Q1. How does HashSet prevent duplicates?**  
It uses hashCode to find a bucket and equals to identify an existing equal element.

**Q2. HashSet vs TreeSet?**  
HashSet is hash-based with expected constant-time operations; TreeSet is sorted and generally logarithmic.

**Q3. Queue vs Deque?**  
Queue focuses on head/tail processing; Deque supports both ends.

**Q4. Is PriorityQueue FIFO?**  
No. It orders by priority; equal-priority ordering is not generally guaranteed.

**Q5. Why might TreeSet reject two unequal objects?**  
Its comparator may return zero, which means they occupy the same ordering position for set uniqueness.

**Q6. Why is ArrayDeque often preferred to Stack?**  
It has a modern Deque API and avoids Stack's legacy synchronized Vector inheritance. It is not itself thread-safe.

## 10. Practice

1. Remove duplicates while preserving insertion order.
2. Use TreeMap to find records within an ID range.
3. Implement a stack and FIFO queue with ArrayDeque.
4. Use PriorityQueue to find the k largest values.
5. Create a TreeSet comparator that accidentally returns zero for distinct objects and explain the result.
