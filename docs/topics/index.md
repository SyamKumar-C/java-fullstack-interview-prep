# Topic Catalogue

This page is the navigation catalogue for the 12 study areas. Detailed topic lessons will be added in Phase 2.

## Core Java

- OOP principles: encapsulation, inheritance, polymorphism, abstraction
- Classes, objects, constructors, this, and super
- Overloading vs overriding
- Abstract classes vs interfaces
- Interface default, static, and private methods
- Access modifiers and packages
- String immutability and String pool
- equals() and hashCode()
- Wrapper types, autoboxing, and unboxing
- Exception hierarchy and custom exceptions
- Generics, wildcards, and bounded types
- Records, enums, and nested classes

## Collections

- Collection hierarchy
- ArrayList vs LinkedList
- HashSet, LinkedHashSet, TreeSet
- HashMap, LinkedHashMap, TreeMap, ConcurrentHashMap
- Hashing, collisions, buckets, and resizing
- Comparable vs Comparator
- Iterator, ListIterator, fail-fast behavior
- Immutable vs unmodifiable collections
- Collection time complexity and selection

## Streams and Functional Programming

- Functional interfaces, lambdas, method references
- Stream creation, lazy evaluation, pipeline execution
- Intermediate vs terminal operations
- map, filter, flatMap
- distinct, sorted, limit, skip
- findFirst, findAny, anyMatch, allMatch, noneMatch
- reduce and collect
- groupingBy, partitioningBy, counting, mapping
- Optional and parallel streams

## Concurrency and JVM

- Process vs thread, thread lifecycle
- Runnable vs Callable
- synchronized, volatile, atomic classes
- Locks, race conditions, deadlocks, starvation
- ExecutorService, thread pools, CompletableFuture
- Concurrent collections and Java Memory Model
- Platform threads vs virtual threads
- Heap, stack, metaspace, garbage collection
- Memory leaks and OutOfMemoryError

## Spring and Spring Boot

- IoC, dependency injection, bean lifecycle and scopes
- Component scanning, configuration, auto-configuration
- Starters and conditional configuration
- Profiles and externalized configuration
- REST controllers, validation, exception handling
- Filters, interceptors, AOP
- Transactions, propagation, isolation
- Async and scheduled execution
- Actuator, logging, health checks

## JPA and Hibernate

- Entity lifecycle and persistence context
- Entity relationships and owning side
- Fetch types, N+1 problem, entity graphs, fetch joins
- Dirty checking and flushing
- Cascades and orphan removal
- JPQL, Criteria API, native queries
- Optimistic and pessimistic locking
- Transaction boundaries and batch operations

## SQL and Databases

- Joins, subqueries, correlated subqueries
- CTEs and recursive CTEs
- Window functions
- Constraints and normalization
- B-tree, composite, covering, partial, expression indexes
- Selectivity, cardinality, EXPLAIN plans
- ACID, isolation levels, transaction anomalies
- Locks, deadlocks, pagination

## REST APIs and Security

- REST constraints, resources, HTTP methods and status codes
- Idempotency, safe retries, pagination, filtering
- Validation and error response design
- Authentication vs authorization
- Sessions, JWT, refresh tokens
- OAuth 2.0 and OpenID Connect
- CORS, CSRF, password hashing, secrets
- API rate limiting

## Microservices and Distributed Systems

- Monolith vs microservices and service boundaries
- Domain ownership, API Gateway, service discovery
- Synchronous vs asynchronous communication
- Kafka topics, partitions, consumer groups
- Delivery semantics and idempotent consumers
- Retries, timeouts, circuit breakers, bulkheads
- Saga and transactional outbox
- Distributed tracing, caching, multi-tenancy
- Consistency and availability trade-offs

## Angular

- Components, templates, lifecycle hooks
- Standalone components and dependency injection
- Inputs, outputs, directives, pipes
- Routing, lazy loading, route guards
- Reactive forms and validation
- HttpClient and interceptors
- Change detection, OnPush, signals
- RxJS interop, state patterns, performance

## JavaScript, TypeScript and RxJS

- Scope, hoisting, closures, this, prototypes
- Event loop, call stack, task queues
- Promise, async/await, error handling
- TypeScript types, interfaces, unions, generics
- Type narrowing and utility types
- Observable vs Promise
- Subject, BehaviorSubject, ReplaySubject
- switchMap, mergeMap, concatMap, exhaustMap
- combineLatest, forkJoin, zip
- catchError, retry, debounceTime, shareReplay
- Subscription lifecycle and memory leaks

## System Design, Cloud and DevOps

- Requirements, constraints, capacity estimation
- Load balancing, horizontal scaling, caching
- Database scaling and replication
- Object storage and file-upload design
- Email, notification, reporting service ownership
- Docker, CI/CD, deployment strategies
- AWS EC2, S3, RDS, ECS/EKS, CloudFront
- Configuration, secrets, observability
- High availability and disaster recovery

## Coverage additions in this audit
- Spring Boot: auto-configuration, testing, async execution, scheduling, caching, Actuator
- JPA: JPQL, Criteria API, projections, pagination, batching
- SQL: constraints, grouping, NULL semantics, views, materialized views
- APIs: OAuth 2.0, OIDC, rate limiting, idempotency keys
- Microservices: Kafka partitions, consumer groups, offsets, delivery semantics

- Build tooling and Git workflows
- GraphQL and schema federation
- Spring testing and advanced security
- Java coding interview patterns
- Kubernetes application deployment
