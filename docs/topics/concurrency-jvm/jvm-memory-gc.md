# JVM Memory Areas, Garbage Collection and Memory Leaks

## 1. JVM runtime memory areas

The JVM uses several runtime memory areas. The key interview distinction is which are shared and which are per-thread.

| Area | Scope | Main purpose |
|---|---|---|
| Heap | Shared | Objects and arrays |
| Java stack | Per thread | Stack frames, local variables and method execution |
| PC register | Per thread | Current JVM instruction position |
| Native method stack | Per thread/runtime implementation | Native method execution |
| Metaspace | Shared native memory | Class metadata |
| Code cache | Shared | JIT-compiled native code |

A local variable can hold a reference to an object; the object is generally allocated on the heap. Do not assume every local variable is always physically stored on the stack: JIT optimizations can change implementation details.

## 2. Stack vs heap

| Stack | Heap |
|---|---|
| Each thread has its own stack | Shared by application threads |
| Contains method frames and local execution state | Contains objects and arrays |
| Frames are removed as calls return | Objects are reclaimed when no longer reachable |
| Exhaustion can cause StackOverflowError | Exhaustion can cause OutOfMemoryError |

Deep or unbounded recursion can overflow a thread's stack. A memory leak usually means objects remain reachable when the application no longer needs them, not simply that GC failed to run.

## 3. Heap generations and allocation

Many HotSpot collectors organize heap space around generational ideas because many objects become unreachable young. Exact layout and algorithms depend on the selected collector and JDK version.

Common terms:
- Young generation: newly allocated objects; minor/young collections reclaim short-lived objects.
- Old generation: objects that survive long enough may be promoted.
- Eden and survivor spaces: common terms in generational collectors.
- Humongous objects: in G1, objects large relative to a region may receive special handling.

Do not describe one collector's internal layout as a universal JVM rule.

## 4. How garbage collection works

GC reclaims objects that are no longer reachable from GC roots. Typical roots include references from active thread stacks/registers, static references and JNI handles.

At a high level:
1. Identify reachable objects starting from roots.
2. Determine which objects are no longer reachable.
3. Reclaim or compact memory according to the collector's algorithm.

Some phases may pause application threads (stop-the-world). Collectors aim to balance throughput, pause time, footprint and responsiveness.

## 5. Common garbage collectors

The available collectors depend on the JDK and runtime configuration.

- **Serial GC:** simple, single-threaded collector; can suit small heaps or constrained environments.
- **Parallel GC:** focuses on throughput using parallel collection work.
- **G1 GC:** region-based collector designed to balance throughput and pause-time goals; commonly used as a default in many server JDK configurations.
- **ZGC:** low-latency collector designed to keep pauses very short, with trade-offs in resource use and configuration.
- **Shenandoah:** low-pause collector available in supported OpenJDK distributions.

The default collector can vary by JDK version, vendor and environment. Know how to check the actual runtime rather than memorizing one universal default.

## 6. Stop-the-world and GC metrics

A stop-the-world pause suspends application threads for a GC phase. Low pause time is not the only goal: throughput, memory footprint and allocation rate also matter.

Monitor:
- Heap used/committed/max.
- Allocation rate and promotion rate.
- GC count and pause duration.
- Old-generation occupancy after collection.
- Time spent in GC.
- Metaspace and direct/native memory.
- Application latency and throughput.

A high GC count alone does not prove a memory leak. Interpret it with allocation rate, pause times and post-GC occupancy.

## 7. OutOfMemoryError vs StackOverflowError

- `OutOfMemoryError: Java heap space`: heap cannot satisfy an allocation.
- `OutOfMemoryError: Metaspace`: class metadata space is exhausted.
- `OutOfMemoryError: unable to create native thread`: OS/process resources or memory prevent thread creation.
- `StackOverflowError`: a thread's stack cannot grow for another frame.

Other OOME messages can involve direct buffers or GC overhead. Diagnose the specific message and runtime metrics.

## 8. Memory leaks in Java

Java can have memory leaks even with garbage collection. If an object is still reachable, GC cannot reclaim it.

Common causes:
- Static collections that keep adding entries.
- Unbounded caches without eviction.
- Listeners or callbacks that are never removed.
- ThreadLocal values retained by long-lived pool threads.
- Queues that grow faster than consumers process them.
- References retained by sessions, request contexts or class loaders.

Example: a static Map used as a cache with no size limit can retain every key/value indefinitely.

## 9. Diagnosing memory problems

A practical investigation:
1. Confirm the exact error and capture JVM/container memory limits.
2. Check heap, metaspace, direct memory, thread count and GC logs/metrics.
3. Capture a heap dump when safe and compare retained objects.
4. Inspect dominator tree and paths to GC roots using a heap-analysis tool.
5. Check thread dumps for excessive threads or blocked work.
6. Fix retention or allocation behavior; do not simply increase heap without understanding the cause.

Useful tools include `jcmd`, `jstack`, `jmap`, Java Flight Recorder (JFR), GC logs and heap-dump analyzers. Commands and flags vary by JDK version.

## 10. JVM options to recognize

- `-Xms`: initial/minimum heap size.
- `-Xmx`: maximum heap size.
- `-Xss`: thread stack size.
- `-XX:MaxMetaspaceSize`: cap for metaspace.
- `-Xlog:gc*`: unified GC logging in modern JDKs (for example Java 9+).

In containers, account for the container memory limit, not only `-Xmx`: metaspace, thread stacks, direct buffers, JIT/code cache and other native memory also consume the process budget.

## 11. Common mistakes

1. Saying all Java memory is on the heap.
2. Saying local variables always physically live on the stack.
3. Assuming GC can collect every object not currently used by business logic; reachability controls collection.
4. Calling every OutOfMemoryError a heap problem.
5. Assuming one collector is always the default.
6. Increasing `-Xmx` without checking container memory.
7. Treating high GC count alone as proof of a leak.
8. Forgetting ThreadLocal, static cache and listener retention.

## 12. Interview questions

**Q1. Stack vs heap?** Stack is per-thread and stores call frames; heap is shared and stores objects and arrays.

**Q2. What makes an object eligible for GC?** It is no longer reachable from GC roots.

**Q3. Can Java have memory leaks?** Yes. Objects can remain reachable through static fields, caches, listeners, ThreadLocals or queues even when no longer needed.

**Q4. What is stop-the-world?** A GC phase during which application threads are paused.

**Q5. OutOfMemoryError vs StackOverflowError?** OOME indicates a memory/resource allocation failure; StackOverflowError indicates stack exhaustion, often due to deep recursion.

**Q6. What is a GC root?** A starting reference used by the collector to determine reachability, such as a live thread reference or static reference.

**Q7. Does increasing Xmx always fix OOME?** No. The issue may be a leak, native memory, thread count, metaspace, direct buffers or a container limit.

**Q8. What would you inspect for a production memory leak?** GC metrics/logs, heap dump, retained objects, paths to GC roots, static caches, ThreadLocals, listeners and queue growth.

## 13. Practice

1. Explain which memory area is per-thread and which is shared.
2. Create a static collection that grows and explain why GC cannot reclaim its contents.
3. Compare a heap OOME with StackOverflowError.
4. Explain why container memory must be larger than the configured maximum heap.
5. Read a GC log and identify pause time, heap occupancy and collection type.
