# JVM Memory Areas and Garbage Collection

## 1. JVM runtime memory areas

- Heap: stores objects and arrays; shared by threads.
- Java stacks: each thread has frames for method calls, local variables and partial results.
- Program Counter (PC) register: per-thread execution position.
- Native method stacks: support native method execution.
- Metaspace: stores class metadata in native memory.
- Code cache: stores JIT-compiled native code.

Static fields are associated with class metadata/class runtime structures, but do not describe the entire object storage model; referenced objects are generally on the heap.

## 2. Stack vs heap

| Stack | Heap |
|---|---|
| Per-thread | Shared |
| Method frames/local execution data | Objects and arrays |
| Automatically unwound as calls return | Reclaimed by GC when unreachable |
| StackOverflowError if exhausted | OutOfMemoryError if exhausted |

A local variable may hold a reference to an object on the heap.

## 3. Garbage collection

Garbage collection reclaims heap objects that are no longer reachable from GC roots. Roots include active thread references, static references and JVM-internal references.

GC does not simply delete objects as soon as a variable goes out of scope; reachability and collection timing are controlled by the JVM.

## 4. Generational idea

Many collectors use generational behavior because many objects die young. Young-generation collections are often frequent and relatively short; long-lived objects may be promoted. Exact regions and algorithms depend on the selected collector.

## 5. Common collectors

- Serial GC: simple, single-threaded collection; useful for small heaps/single-CPU cases.
- Parallel GC: throughput-oriented, uses multiple GC threads.
- G1 GC: region-based collector designed to balance pause goals and throughput.
- ZGC: low-latency collector designed for very large heaps and short pauses, with trade-offs depending on workload.

Collector availability and defaults depend on the JDK version and platform.

## 6. Memory errors

- StackOverflowError: often deep or infinite recursion.
- OutOfMemoryError: heap, metaspace, direct buffer or another memory area exhausted.
- Memory leak: objects remain reachable even though the application no longer needs them.

A Java memory leak can occur even though Java has garbage collection.

## 7. Diagnosing memory problems

Use heap dumps, GC logs, Java Flight Recorder, VisualVM or other profilers. Check retained object graphs, collection sizes, allocation rate, GC pause time and native memory. Do not increase -Xmx before understanding the cause.

## 8. Practical scenario

A Spring Boot file-upload endpoint reads many large files fully into byte arrays. Heap usage rises sharply and GC cannot reclaim the arrays while requests hold references. Stream data, limit upload size and concurrency, and inspect heap/allocation profiles.

## 9. Common mistakes

- Saying all variables live on the stack.
- Assuming System.gc() guarantees collection.
- Treating every high heap usage as a leak.
- Increasing heap without checking container memory limits.
- Ignoring direct/native memory and thread-stack usage.

## 10. Interview questions

**Q1. What is the heap?** Shared runtime memory where objects and arrays are allocated.

**Q2. What are GC roots?** References from which object reachability is traced.

**Q3. Can Java have memory leaks?** Yes. Unneeded objects can remain reachable through caches, listeners, static collections or other references.

**Q4. StackOverflowError vs OutOfMemoryError?** StackOverflow commonly indicates excessive call depth; OutOfMemory indicates a memory allocation area cannot satisfy a request.

**Q5. Does System.gc() force GC?** No. It is only a request/hint.

## 11. Practice

Capture a heap dump from a test application that retains objects in a static list. Identify the retaining path and explain why GC cannot collect those objects.
