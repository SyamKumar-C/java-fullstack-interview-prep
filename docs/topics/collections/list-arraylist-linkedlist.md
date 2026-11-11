# List, ArrayList and LinkedList

List is ordered, allows duplicates and supports positional access.

## ArrayList
Uses a resizable array. get(index) is O(1), append is amortized O(1), insertion/removal near the beginning or middle is O(n) because elements shift. Usually the default List choice.

## LinkedList
Uses a doubly linked list. Index access is O(n); adding/removing at ends is O(1), but finding a position still costs O(n). Nodes require extra memory and pointer chasing.

```java
List<String> names = new ArrayList<>();
names.add("A"); names.add("B");
names.remove(0);
System.out.println(names); // [B]
```

Choose ArrayList for general-purpose lists. LinkedList is useful only for specific iterator/end-operation patterns; it is rarely faster in real workloads.

**Interview:** Is ArrayList thread-safe? No. Does capacity equal size? No; size is element count, capacity is backing storage.
