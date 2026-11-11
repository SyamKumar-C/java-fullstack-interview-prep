# Java Concurrent Collections

## 1. Why concurrent collections?

A normal collection such as ArrayList or HashMap is not designed for unsynchronized concurrent modification by multiple threads. Concurrent collections provide thread-safe operations with defined concurrency behavior.

Thread-safe does not automatically mean every multi-step business operation is atomic. Choose the collection based on the operation and consistency needed.

## 2. ConcurrentHashMap

ConcurrentHashMap supports concurrent reads and updates. It does not allow null keys or values. Individual operations such as `putIfAbsent`, `compute` and `merge` are atomic for the relevant key.

```java
ConcurrentMap<String, Long> counts = new ConcurrentHashMap<>();
counts.merge("login", 1L, Long::sum);
counts.putIfAbsent("status", 0L);
```

Prefer atomic map methods over a separate get-then-put sequence, which can race.

## 3. CopyOnWriteArrayList

Every write creates a fresh backing array. Iterators see a snapshot and do not throw ConcurrentModificationException because another thread updates the list.

Good for many reads and very few writes, such as a listener registry. Poor for frequent writes or very large lists because copying is expensive.

## 4. BlockingQueue

A BlockingQueue supports producer-consumer workflows. A producer can wait when the queue is full, and a consumer can wait when it is empty.

Common implementations:
- `ArrayBlockingQueue`: bounded array-backed queue.
- `LinkedBlockingQueue`: optionally bounded linked queue.
- `PriorityBlockingQueue`: priority ordering, generally unbounded.
- `SynchronousQueue`: direct handoff; no stored elements.

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(100);
queue.put("job-1");       // waits if full
String job = queue.take(); // waits if empty
```

## 5. Other collections

- `ConcurrentLinkedQueue`: non-blocking, unbounded queue.
- `ConcurrentSkipListMap`: concurrent sorted map.
- `Collections.synchronizedList`: synchronized wrapper; iteration still requires external synchronization on the wrapper.

## 6. Choosing a collection

| Requirement | Candidate |
|---|---|
| Concurrent key/value access | ConcurrentHashMap |
| Many reads, rare writes, snapshot iteration | CopyOnWriteArrayList |
| Producer-consumer with backpressure | BlockingQueue |
| Concurrent sorted keys | ConcurrentSkipListMap |
| Simple synchronized wrapper | Collections.synchronizedX |

## 7. Practical scenarios

- Cache counters: ConcurrentHashMap.merge.
- Event listeners: CopyOnWriteArrayList if listener changes are rare.
- Background job workers: bounded BlockingQueue to apply backpressure.
- Rate-limiter state: atomic map operations plus a carefully designed value object.

## 8. Common mistakes

- Assuming a thread-safe collection makes a sequence of operations atomic.
- Using CopyOnWriteArrayList for frequent writes.
- Using an unbounded queue and allowing memory to grow without limit.
- Iterating a synchronized wrapper without synchronizing on it.
- Using ConcurrentHashMap when a database transaction or distributed lock is required across multiple application instances.

## 9. Interview questions

**Q1. HashMap vs ConcurrentHashMap?** HashMap is not safe for concurrent updates; ConcurrentHashMap supports concurrent access and atomic per-key operations.

**Q2. Why does ConcurrentHashMap reject null?** Null would make it unclear whether a null result means no mapping or a mapped null value in concurrent operations.

**Q3. When is CopyOnWriteArrayList useful?** When reads and iteration greatly outnumber writes.

**Q4. What is a BlockingQueue?** A queue whose put/take operations can wait for capacity or data.

## 10. Practice

Build a producer-consumer example with a bounded ArrayBlockingQueue. Then replace a shared HashMap counter with ConcurrentHashMap.merge and explain the race it prevents.
