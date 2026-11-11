# HashMap Internals

HashMap stores entries in buckets. It computes a key hash, maps it to a bucket, then uses hash and equals to find the matching key.

## Simplified put/get
1. Compute/spread hash.
2. Calculate bucket index.
3. Insert if empty.
4. On collision, compare hashes and equals; update a matching key or add another node.
5. Resize when size crosses capacity/load-factor threshold.

Average get/put is O(1); collisions can make operations slower. Modern Java HashMap can treeify heavily-collided bins under certain capacity/threshold conditions.

## Contract and behavior
Equal keys must have equal hash codes. Do not mutate fields used by equals/hashCode while a key is in the map. HashMap permits one null key and null values, and does not guarantee iteration order.

```java
Map<String,Integer> counts = new HashMap<>();
counts.merge("java", 1, Integer::sum);
counts.merge("java", 1, Integer::sum);
System.out.println(counts.get("java")); // 2
```

**Interview:** Why resize? To keep bucket occupancy manageable. Is HashMap thread-safe? No; consider ConcurrentHashMap.
