# Study Roadmap

Use this as a checklist. Mark a topic complete only when you can explain it, write a small example, and handle follow-up questions.

## 1. Core Java
- OOP: encapsulation, inheritance, polymorphism, abstraction
- Classes, objects, constructors, this, and super
- Method overloading and overriding
- Abstract classes vs interfaces
- Default, static, and private interface methods
- Access modifiers and packages
- String immutability and String pool
- equals() and hashCode()
- Wrapper classes, autoboxing, and unboxing
- Checked vs unchecked exceptions
- Generics, wildcards, and bounded types
- Records, enums, and nested classes

## 2. Collections
- Collection hierarchy
- ArrayList vs LinkedList
- HashSet, LinkedHashSet, and TreeSet
- HashMap, LinkedHashMap, TreeMap, and ConcurrentHashMap
- HashMap internals, hashing, collisions, and resizing
- Comparable vs Comparator
- Iterator, ListIterator, fail-fast behavior
- Immutable vs unmodifiable collections

## 3. Streams and Functional Programming
- Functional interfaces: Predicate, Function, Consumer, Supplier, primitive specializations
- Lambda syntax, target typing, method references, effectively final capture and composition
- Stream sources: Collection, arrays, Stream.of, iterate, generate and primitive streams
- Intermediate operations: filter, map, flatMap, distinct, sorted, peek, limit, skip, takeWhile, dropWhile
- Terminal operations: toList, collect, forEach, count, reduce, min, max, findFirst, findAny
- Lazy evaluation, short-circuiting, encounter order and stream reuse
- map vs flatMap; reduce vs collect
- Collectors: groupingBy, partitioningBy, counting, mapping, summing, averaging, summarizing and joining
- toMap duplicate-key merge functions and collectingAndThen
- Optional: of, ofNullable, map, flatMap, filter, orElse, orElseGet and orElseThrow
- Parallel stream splitting, common pool, ordering, associative reductions and performance trade-offs
- Java 8/9/16 API version differences

## 4. Concurrency and JVM
- Process vs thread and thread lifecycle
- Runnable vs Callable
- synchronized, volatile, and atomic classes
- Locks, race conditions, deadlocks, and starvation
- ExecutorService and thread pools
- CompletableFuture
- Concurrent collections
- Java Memory Model and happens-before
- Platform threads vs virtual threads
- Heap, stack, metaspace, and garbage collection
- Memory leaks and OutOfMemoryError

## 5. SQL and Databases
- Joins, subqueries, CTEs, and window functions
- Primary, foreign, unique, and check constraints
- B-tree, composite, covering, partial, and expression indexes
- Selectivity, cardinality, and query plans
- Transactions and ACID
- Isolation levels and transaction anomalies
- Normalization and denormalization
- Locks, deadlocks, optimistic and pessimistic concurrency
- Pagination strategies

## 6. Spring and Spring Boot
- IoC and dependency injection
- Bean lifecycle and scopes
- Component scanning and configuration
- Auto-configuration and starters
- Profiles and externalized configuration
- REST controllers and request mapping
- Validation and exception handling
- Filters, interceptors, and AOP
- Transactions and propagation
- Async and scheduled execution
- Actuator, logging, and health checks

## 7. Persistence and APIs
- JPA entities and relationships
- Fetch types, N+1, entity graphs, and joins
- Persistence context and dirty checking
- Cascades and orphan removal
- JPQL, Criteria API, and native queries
- REST design, status codes, idempotency, and pagination
- Authentication vs authorization
- JWT, OAuth 2.0, CORS, and CSRF
- Unit, slice, and integration testing

## 8. Microservices and System Design
- Service boundaries and domain ownership
- API Gateway and service discovery
- Synchronous vs asynchronous communication
- Kafka: partitions, consumer groups, and delivery semantics
- Retries, timeouts, circuit breakers, and bulkheads
- Saga, outbox, and idempotent consumers
- Distributed tracing, metrics, and centralized logs
- Caching and cache invalidation
- Multi-tenancy and tenant context
- Availability, consistency, and scalability trade-offs

## 9. Frontend
- JavaScript scope, closures, hoisting, and prototypes
- Promise, async/await, and event loop
- TypeScript types, interfaces, generics, and narrowing
- Angular components, templates, and lifecycle
- Standalone components, dependency injection, and routing
- Reactive forms and validation
- RxJS Observable, Subject, BehaviorSubject, and ReplaySubject
- RxJS operators: map, switchMap, mergeMap, concatMap, exhaustMap, catchError, combineLatest, forkJoin, debounceTime, distinctUntilChanged, shareReplay
- Angular signals, change detection, and performance
- HTTP interceptors and route guards

## 10. Delivery and Practice
- Git branching, commits, and pull requests
- Maven/Gradle build lifecycle
- Docker images and containers
- AWS: EC2, S3, RDS, ECS/EKS, CloudFront, SES
- CI/CD and deployment strategies
- Coding: arrays, strings, maps, trees, graphs, BFS, and DFS
- System design exercises and project deep-dives

## Additional coverage checklist
- [ ] Spring Boot auto-configuration conditions and testing slices
- [ ] Async executor sizing, scheduling across replicas, cache invalidation, Actuator security
- [ ] JPQL, Criteria API, DTO projections, Page vs Slice, JDBC batching
- [ ] SQL constraints, GROUP BY/HAVING, NULL behavior, views and materialized views
- [ ] REST resource design, OAuth 2.0/OIDC, rate limiting, idempotency keys
- [ ] Kafka partitions, keys, consumer groups, offsets, retries and dead-letter topics
- [ ] Angular directives, pipes, DI scopes, guards, resolvers, lazy loading and state
- [ ] Collection hierarchy, Map variants, unmodifiable views and fail-fast iterators
- [ ] JavaScript array mutation, async error handling and browser storage
- [ ] System design requirements, capacity estimation, file storage, notification ownership, Docker and CI/CD

## Additional interview readiness checklist
- [ ] Maven/Gradle lifecycle, dependency scopes, BOMs and dependency conflicts
- [ ] Git staging, commits, merge/rebase, conflict resolution, revert/reset/reflog
- [ ] JUnit, Mockito, Spring test slices, Testcontainers and integration testing
- [ ] Spring Security filter chain, method security, JWT validation, CSRF/CORS
- [ ] GraphQL schema, resolvers, N+1, DataLoader and federation
- [ ] Coding patterns: hash maps, two pointers, sliding window, stack, binary search, BFS/DFS
- [ ] Kubernetes Pods, Deployments, Services, probes, requests/limits and rollout troubleshooting
