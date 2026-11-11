# Concurrent Collections

Ordinary HashMap, ArrayList and HashSet are not safe for concurrent mutation without coordination.

## ConcurrentHashMap
Supports concurrent retrieval and updates; rejects null keys and values. Use atomic methods such as putIfAbsent, compute, computeIfAbsent and merge instead of check-then-act logic.

## CopyOnWriteArrayList
Each write creates a new backing array; iterators see a snapshot. Useful for many reads and very few writes (for example listener lists), not frequent writes or large collections.

## BlockingQueue
Supports producer-consumer coordination. put() waits when full; take() waits when empty. ArrayBlockingQueue is bounded; LinkedBlockingQueue can be bounded or effectively unbounded.

**Interview:** Why does ConcurrentHashMap reject null? Null would make absence ambiguous in concurrent lookup/update semantics. When use CopyOnWriteArrayList? Read-heavy, low-write workloads.
