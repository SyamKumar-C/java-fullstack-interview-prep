# HashSet, TreeSet, Queue and Deque

## HashSet
Stores unique elements using a HashMap internally. Expected O(1) add/remove/contains; iteration order is not guaranteed. equals/hashCode must be correct.

## TreeSet
Stores sorted unique elements using a balanced tree. Operations are O(log n). Requires natural ordering or a Comparator.

## Queue and Deque
Queue often represents FIFO processing. Deque supports both ends and can act as queue or stack. Prefer offer/poll/peek when empty/full should be handled without exceptions.

```java
Deque<String> stack = new ArrayDeque<>();
stack.push("first"); stack.push("second");
System.out.println(stack.pop()); // second
```

ArrayDeque is usually preferred over legacy Stack. It does not allow null.

## PriorityQueue
A heap: peek returns the head by natural order or comparator. Iteration is not sorted. offer/poll are O(log n), peek O(1).

**Interview:** HashSet vs TreeSet? Hash-based uniqueness vs sorted uniqueness. Is PriorityQueue fully sorted? No.
