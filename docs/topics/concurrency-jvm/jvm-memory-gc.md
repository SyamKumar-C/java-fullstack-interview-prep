# JVM Memory Areas and Garbage Collection

## Runtime areas
- Heap: objects and arrays, shared by threads.
- Java stacks: per-thread frames and local call state.
- Metaspace: class metadata in native memory.
- Code cache: compiled native code.
- Other native memory: JVM structures, direct buffers and libraries.

Exact implementation is JVM-specific; this is a useful HotSpot mental model.

## Garbage collection
GC reclaims heap objects no longer reachable from GC roots (such as active thread stacks, static references and JNI references). Going out of scope does not mean immediate collection. System.gc() is only a request/hint.

## Leaks
Java can leak memory when unwanted objects remain reachable: unbounded caches, static collections, listener registrations or ThreadLocal values not cleared in pooled threads.

**Interview:** Can Java have memory leaks? Yes. GC removes unreachable objects, not objects that remain reachable but are no longer useful.
