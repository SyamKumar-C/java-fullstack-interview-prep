# Spring and Spring Boot

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 3–4 hours

Spring provides infrastructure for building Java applications. Spring Boot adds opinionated defaults, auto-configuration, and production-ready features.

## 1. IoC and dependency injection

Inversion of Control means the framework manages object creation and wiring. Dependency Injection supplies an object's required collaborators from outside.

Prefer constructor injection because dependencies are explicit and can be final.

```java
@Service
class OrderService {
    private final PaymentService paymentService;

    OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

## 2. Bean lifecycle and scopes

A Spring bean is an object managed by the IoC container. Common scopes:

- singleton: one bean instance per container (default).
- prototype: a new instance when requested from the container.
- request: one instance per HTTP request in a web application.
- session: one instance per HTTP session.

Singleton does not mean one instance across all JVMs. A singleton bean should generally be stateless or designed for thread safety.

## 3. Auto-configuration and starters

Starters provide dependency groups. Auto-configuration registers beans based on classpath contents, existing beans, and configuration conditions. It can be overridden by defining your own beans or changing properties.

## 4. Profiles and external configuration

Profiles allow environment-specific configuration, such as application-dev.yml and application-prod.yml. Externalize secrets and environment-specific values instead of hardcoding them.

## 5. REST APIs

Use @RestController for HTTP endpoints, request DTOs for input, Bean Validation for constraints, and a consistent exception handler such as @RestControllerAdvice.

```java
@RestController
@RequestMapping("/api/orders")
class OrderController {
    private final OrderService service;

    OrderController(OrderService service) {
        this.service = service;
    }

    @PostMapping
    ResponseEntity<OrderResponse> create(
            @Valid @RequestBody CreateOrderRequest request) {
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(service.create(request));
    }
}
```

## 6. Filters, interceptors, and AOP

- Filter: Servlet-level processing around requests, often used for security or request wrapping.
- HandlerInterceptor: Spring MVC processing around controller handler execution.
- AOP: Cross-cutting behavior around method execution, such as transactions or auditing.

Choose based on where the behavior belongs. Do not use an MVC interceptor as a replacement for a security filter chain.

## 7. Transactions

@Transactional defines a transaction boundary. Understand propagation (how a method joins or creates a transaction) and isolation (what concurrent transactions can observe).

Common pitfalls:
- In proxy-based Spring AOP, self-invocation may bypass transactional interception.
- By default, rollback occurs for unchecked exceptions and Error, not every checked exception.
- Long transactions hold resources and locks longer.
- A transaction does not automatically cover a remote HTTP call.

## 8. Async and scheduling

@EnableAsync enables asynchronous method execution with @Async. Configure an executor for control over thread count and queue capacity. @Scheduled runs tasks according to a schedule; configure timezone where needed.

## 9. Actuator and operations

Spring Boot Actuator provides health, metrics, and other management endpoints. Expose only what is needed and secure sensitive endpoints.

## 10. Interview questions

??? question "Why is constructor injection preferred?"
    It makes required dependencies explicit, supports immutability with final fields, and makes unit testing easier. It also avoids partially initialized objects.

??? question "What is the difference between @Component, @Service, and @Repository?"
    All register Spring-managed components. @Service communicates service-layer intent. @Repository communicates persistence intent and can participate in persistence exception translation.

??? question "What is Spring Boot auto-configuration?"
    It conditionally configures beans based on the classpath, existing beans, and properties to reduce manual setup. It can be customized or replaced.

??? question "Why might @Transactional not work on a method call inside the same class?"
    Proxy-based transaction management intercepts calls that pass through the Spring proxy. A self-invocation calls the method directly on this and bypasses that proxy.

??? question "Does @Async automatically make a method safe?"
    No. It changes where execution happens. Shared state still needs thread-safety, and executor capacity, exception handling, and transaction boundaries must be considered.

## 11. Practice

1. Build a REST endpoint with validation and a global exception handler.
2. Configure dev and prod profiles.
3. Explain filter vs interceptor vs AOP with examples.
4. Demonstrate transaction rollback and propagation.
