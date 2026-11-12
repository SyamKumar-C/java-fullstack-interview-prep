# Java Concurrent Collections

## 1. Why concurrent collections?

ArrayList, HashMap and most ordinary collections do not support unsynchronized concurrent structural updates. Concurrent collections provide defined thread-safe operations and iteration behavior.

**Thread-safe does not mean every multi-step workflow is atomic.** Choose the collection based on the operation, consistency and backpressure requirements.

## 2. ConcurrentHashMap

ConcurrentHashMap supports concurrent reads and updates. It rejects null keys and values. It does not lock the entire map for every operation; modern implementations coordinate updates at a finer level and support concurrent reads.

Useful atomic per-key methods include:
- `putIfAbsent`
- `computeIfAbsent`, `computeIfPresent`, `compute`
- `merge`
- `replace`, conditional `remove`

```java
ConcurrentMap<String, Long> counts = new ConcurrentHashMap<>();
counts.merge("login", 1L, Long::sum);
counts.putIfAbsent("status", 0L);
```

Avoid a separate get-then-put sequence for a shared counter; two threads can read the same old value and overwrite each other's update.

### Why does ConcurrentHashMap reject null?

A null return from get can safely mean there is no mapping. Allowing null values would make absence harder to distinguish during concurrent operations.

### Iteration

Iterators are weakly consistent: they can reflect some updates made after iteration starts, do not throw ConcurrentModificationException merely because of concurrent updates, and are not a fixed snapshot.

## 3. CopyOnWriteArrayList

Every write creates a new backing array. Readers and iterators use the array snapshot captured for that operation/iterator.

Use it when reads and iteration are frequent and writes are rare, for example a listener registry. Avoid it for large lists with frequent writes because each update copies the array.

## 4. BlockingQueue and producer-consumer

BlockingQueue supports producer-consumer workflows. A producer can wait when a bounded queue is full; a consumer can wait when it is empty.

| Implementation | Characteristics |
|---|---|
| ArrayBlockingQueue | Bounded, array-backed |
| LinkedBlockingQueue | Linked; optionally bounded |
| PriorityBlockingQueue | Priority order; effectively unbounded |
| SynchronousQueue | Direct handoff; stores no elements |
| DelayQueue | Elements become available after their delay |

```java
BlockingQueue<String> jobs = new ArrayBlockingQueue<>(100);
jobs.put("job-1");       // waits while full
String job = jobs.take(); // waits while empty
```

A bounded queue provides backpressure. An unbounded queue can grow until the application runs out of memory if producers outpace consumers.

## 5. Other concurrent collections

- ConcurrentLinkedQueue: non-blocking unbounded queue.
- ConcurrentSkipListMap: concurrent sorted map with expected logarithmic operations.
- Collections.synchronizedList: synchronized wrapper; iteration requires external synchronization on the wrapper.
- Vector and Hashtable: legacy synchronized collections; modern alternatives are usually clearer.

## 6. Selection guide

| Requirement | Candidate |
|---|---|
| Concurrent key/value access | ConcurrentHashMap |
| Many reads, rare writes, snapshot iteration | CopyOnWriteArrayList |
| Producer-consumer with bounded backpressure | ArrayBlockingQueue or bounded LinkedBlockingQueue |
| Concurrent sorted keys | ConcurrentSkipListMap |
| Non-blocking concurrent queue | ConcurrentLinkedQueue |
| Existing API needs a synchronized wrapper | Collections.synchronizedX |

## 7. Practical project examples

- Shared in-memory counters: ConcurrentHashMap.merge.
- Event listener list: CopyOnWriteArrayList if registrations change rarely.
- Background job workers: bounded BlockingQueue with a defined rejection/backpressure policy.
- Concurrent in-memory index: ConcurrentHashMap with immutable values.
- Distributed coordination across multiple application instances: use a database/Redis/broker coordination design, not only an in-process concurrent collection.

## 8. Atomicity traps

This is not atomic as a whole:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Use `putIfAbsent` for that per-key operation. Likewise, a concurrent map does not make a mutable object stored as its value thread-safe. Synchronize or use immutable/atomic value objects.

## 9. Interview questions

**Q1. HashMap vs ConcurrentHashMap?**  
HashMap is not safe for concurrent updates. ConcurrentHashMap supports concurrent operations and atomic per-key methods.

**Q2. Why does ConcurrentHashMap reject null?**  
It keeps null from being ambiguous with an absent mapping during concurrent lookups.

**Q3. What is weakly consistent iteration?**  
The iterator can reflect some concurrent changes, does not fail fast for those changes, and is not a fixed snapshot.

**Q4. When is CopyOnWriteArrayList useful?**  
When reads vastly outnumber writes and snapshot iteration is acceptable.

**Q5. What is BlockingQueue?**  
A queue with blocking operations such as put/take that wait for capacity or an available item.

**Q6. Why use a bounded queue?**  
It limits memory growth and applies backpressure when consumers cannot keep up.

**Q7. Does ConcurrentHashMap make its values thread-safe?**  
No. The value object needs its own immutability or concurrency control.

**Q8. Is ConcurrentHashMap a distributed cache?**  
No. It is local to one JVM. Multiple application instances have separate maps.

## 10. Practice

1. Implement a frequency counter with ConcurrentHashMap.merge.
2. Build producer and consumer threads using a bounded ArrayBlockingQueue.
3. Demonstrate CopyOnWriteArrayList iterator snapshot behavior.
4. Show why containsKey followed by put is a race and replace it with putIfAbsent.
