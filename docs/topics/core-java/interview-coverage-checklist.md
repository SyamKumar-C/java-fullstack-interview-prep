# Core Java Interview Coverage Checklist

Use this checklist to verify understanding, not to count pages. A topic is complete only when you can explain the rule, predict edge cases, write a small example, and answer follow-up questions without memorizing a paragraph.

## OOP and object model

- [ ] Class, object, reference, state and behavior
- [ ] Encapsulation and protecting invariants (not just getters/setters)
- [ ] Abstraction through interfaces, abstract classes and APIs
- [ ] Inheritance, single class inheritance and multiple interface implementation
- [ ] Runtime polymorphism and dynamic method dispatch
- [ ] Overloading versus overriding; compile-time versus runtime selection
- [ ] Composition versus inheritance; association, aggregation and composition
- [ ] Coupling, cohesion and substitutability
- [ ] SOLID: SRP, OCP, LSP, ISP and DIP
- [ ] Dependency inversion versus dependency injection
- [ ] Practical Spring example using interface-based constructor injection

## Classes, constructors and initialization

- [ ] Class members versus instance members
- [ ] Object creation and default field values
- [ ] Default constructor versus compiler-provided no-argument constructor
- [ ] Constructor overloading and constructor chaining with this() and super()
- [ ] Private constructors and static factory methods
- [ ] Field initializers and instance initializer blocks
- [ ] Static field initializers and static blocks
- [ ] Parent/child initialization order
- [ ] Why calling overridable methods from constructors is dangerous
- [ ] this, super, static and instance context

## Java language fundamentals

- [ ] Primitive types and wrapper classes
- [ ] Autoboxing, unboxing and null-unboxing NPE
- [ ] Wrapper caching and why == is not value equality
- [ ] Java pass-by-value, including copied object references
- [ ] final variable/reference versus immutable object
- [ ] static state and class-loader/JVM scope
- [ ] String immutability, String pool, intern(), == and equals()
- [ ] StringBuilder versus StringBuffer
- [ ] Records: generated members, canonical/compact constructors, shallow immutability and limitations
- [ ] Enums with fields/methods, valueOf(), values(), ordinal() risks and interfaces
- [ ] Static nested, inner, local and anonymous classes
- [ ] Access levels, package-private and protected across packages

## Object contracts and copying

- [ ] Object methods: equals(), hashCode(), toString(), getClass(), clone(), wait(), notify(), notifyAll()
- [ ] equals() contract: reflexive, symmetric, transitive, consistent and non-null
- [ ] hashCode() contract and collision meaning
- [ ] Why equal keys need equal hash codes in HashMap/HashSet
- [ ] Mutable map-key failure
- [ ] Records and generated value equality
- [ ] Shallow versus deep copy
- [ ] Cloneable marker interface and Object.clone() behavior
- [ ] Copy constructors, factories and defensive copies
- [ ] Immutability and safe sharing

## Type system and modern Java

- [ ] Abstract class versus interface
- [ ] Interface abstract/default/static/private methods (Java 8/9)
- [ ] Default method conflict resolution
- [ ] Functional interface and SAM rule
- [ ] Predicate, Function, Consumer, Supplier, UnaryOperator and BinaryOperator
- [ ] Lambdas, method references and target typing
- [ ] Effectively final capture and lambda versus anonymous-class this
- [ ] Generics: classes, methods, bounds and type inference
- [ ] Invariance and PECS (? extends / ? super)
- [ ] Type erasure, raw types, bridge methods and generic limitations
- [ ] Annotations: retention, target, inherited/documented and custom annotations
- [ ] Reflection versus compile-time annotation processing
- [ ] Reflection access/module restrictions and trade-offs

## Exceptions and resource management

- [ ] Throwable hierarchy: Error, checked exception and RuntimeException
- [ ] throw versus throws
- [ ] try/catch/finally behavior and its limits
- [ ] Multi-catch and exception matching order
- [ ] try-with-resources, AutoCloseable and suppressed exceptions
- [ ] Custom exceptions and preserving causes
- [ ] Exception propagation and uncaught-exception handling
- [ ] Designing safe API error responses in Spring
- [ ] Retryable versus non-retryable failures
- [ ] Why not to swallow exceptions or expose stack traces


## Collections and Map implementations

- [ ] Collection hierarchy: Iterable, Collection, List, Set, Queue, Deque; Map is separate
- [ ] ArrayList internals, size versus capacity, resize and amortized append
- [ ] LinkedList doubly linked structure and the cost of finding an insertion position
- [ ] HashMap hash spreading, bucket indexing, put/get and collision handling
- [ ] HashMap capacity, load factor, threshold, resize and tree bins
- [ ] HashMap null behavior, iteration order and fail-fast limitation
- [ ] equals/hashCode contract and mutable-key failure
- [ ] HashSet backing map and uniqueness
- [ ] LinkedHashMap insertion/access order and LRU building-block use
- [ ] TreeMap/TreeSet ordering, range methods and comparator consistency
- [ ] PriorityQueue heap behavior and why iteration is not sorted
- [ ] Queue add/offer, remove/poll, element/peek differences
- [ ] Deque/ArrayDeque for stack and queue use
- [ ] Comparable versus Comparator, chaining and overflow-safe comparisons
- [ ] Iterator versus ListIterator and iterator.remove
- [ ] Fail-fast versus weakly consistent versus snapshot iterators
- [ ] ConcurrentHashMap per-key atomic methods and null restrictions
- [ ] CopyOnWriteArrayList read/write trade-offs
- [ ] BlockingQueue, bounded queues and backpressure
- [ ] Unmodifiable view versus snapshot; shallow versus deep immutability
- [ ] Choose an implementation for a scenario and justify complexity, order, null and thread-safety

## Interview readiness

- [ ] Explain each concept in a 30–60 second answer
- [ ] Draw or describe the internal/runtime behavior where relevant
- [ ] Predict output for a short code snippet
- [ ] Explain at least one real project use case
- [ ] Identify common mistakes and edge cases
- [ ] Solve the practice exercises in the linked lessons
- [ ] Explain trade-offs rather than giving absolute rules

## Related lessons

- [OOP principles and SOLID](oop-principles.md)
- [Classes, objects and constructors](classes-objects-constructors.md)
- [Overloading versus overriding](overloading-vs-overriding.md)
- [Abstract classes versus interfaces](abstract-classes-vs-interfaces.md)
- [Access modifiers and packages](access-modifiers-packages.md)
- [Strings and String Pool](string-immutability-pool.md)
- [equals() and hashCode()](equals-hashcode.md)
- [Wrappers and autoboxing](wrappers-autoboxing.md)
- [Exception handling](exception-handling.md)
- [Generics](generics.md)
- [Records, enums and nested classes](records-enums-nested-classes.md)
- [Pass-by-value, final and static](pass-by-value-final-static.md)
- [Initialization order](initialization-order.md)
- [Cloning and copying](cloning-copying.md)
- [Annotations and reflection](annotations-reflection.md)
- [Functional interfaces and lambdas](functional-interfaces-lambdas.md)
