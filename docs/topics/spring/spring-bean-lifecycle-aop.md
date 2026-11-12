# Spring Bean Lifecycle and AOP

## 1. Lifecycle at a glance
A simplified singleton lifecycle is:
1. Instantiate the object.
2. Populate dependencies and properties.
3. Invoke aware callbacks (where implemented).
4. Run BeanPostProcessor before-initialization callbacks.
5. Run initialization callbacks such as `@PostConstruct`, `InitializingBean.afterPropertiesSet()`, or a configured init method.
6. Run BeanPostProcessor after-initialization callbacks; infrastructure may return a proxy instead of the raw bean.
7. Bean is ready for application use.
8. On context shutdown, run destruction callbacks such as `@PreDestroy`, `DisposableBean.destroy()`, or a configured destroy method.

This is a useful interview-level sequence, not every internal callback in exact implementation order. Prototype beans are created and initialized by Spring, but their destruction is generally the caller's responsibility.

## 2. Lifecycle callback example
```java
@Component
class CacheWarmup {
    @PostConstruct
    void warm() {
        // dependencies have been injected
    }

    @PreDestroy
    void close() {
        // release owned resources
    }
}
```

Use lifecycle callbacks for initialization/cleanup, not for long-running business workflows. Be careful when calling other beans during early startup.

## 3. BeanFactoryPostProcessor vs BeanPostProcessor
- **BeanFactoryPostProcessor:** works with bean-definition metadata before ordinary beans are instantiated; can change definitions.
- **BeanPostProcessor:** works with bean instances before/after initialization; can wrap a bean with a proxy.

Configuration class processing and many annotation-driven features rely on this extension infrastructure.

## 4. AOP: why it exists
Aspect-Oriented Programming separates cross-cutting behavior—transactions, authorization, logging, metrics—from core business logic.

| Term | Meaning |
|---|---|
| Aspect | Module holding cross-cutting behavior |
| Advice | Action executed at a selected point |
| Join point | A point where advice can run |
| Pointcut | Rule selecting join points |
| Target | Original object |
| Proxy | Object through which advised calls pass |

Spring's proxy-based AOP mainly advises method executions on Spring-managed beans.

## 5. Advice types
- `@Before`: before target method.
- `@AfterReturning`: after normal return.
- `@AfterThrowing`: when an exception escapes.
- `@After`: after completion, whether success or failure.
- `@Around`: wraps the call and controls whether the target proceeds.

Around advice must normally call `ProceedingJoinPoint.proceed()`; forgetting it can prevent the target method from running.

## 6. JDK dynamic proxy vs CGLIB
- JDK proxy implements interfaces exposed by the target.
- CGLIB creates a subclass proxy when class-based proxying is used.
- A class-based proxy cannot subclass a final class; final methods cannot be overridden.
- Private methods are not ordinary overridable proxy join points.

The exact proxy choice can depend on Spring configuration and framework version.

## 7. The self-invocation problem
```java
@Service
class InvoiceService {
    public void generate() {
        save(); // direct this.save(): does not pass through proxy
    }

    @Transactional
    public void save() { /* database work */ }
}
```

The external caller enters through the proxy, but the internal call uses the target object's `this` reference. The proxy advice is bypassed. Move the advised method to another bean or redesign the boundary. AspectJ weaving is a different approach.

This limitation can affect `@Transactional`, `@Async`, `@Cacheable`, and method-security annotations.

## 8. Practical examples
- Transaction advice opens/joins a transaction before the target method and commits/rolls back afterward.
- Method security checks authorization before the method executes.
- Cache advice checks the cache and may skip the target method on a hit.
- Async advice submits work to an executor.

## 9. Common mistakes
- Assuming every annotated method is advised regardless of how it is called.
- Treating lifecycle callback order as a license to call complex business logic during startup.
- Expecting Spring to call prototype destruction callbacks automatically.
- Assuming AOP proxying intercepts private or final methods.
- Using broad pointcuts that unintentionally advise unrelated methods.

## 10. Interview questions
**Q1. What is the bean lifecycle?** Creation, dependency population, aware callbacks, post-processing, initialization, use and destruction.

**Q2. BeanFactoryPostProcessor vs BeanPostProcessor?** The former changes bean definitions; the latter works with bean instances and can wrap them.

**Q3. What is AOP?** A way to apply cross-cutting behavior separately from business logic.

**Q4. Why does self-invocation bypass advice?** The call is made directly on the target with `this`, not through the Spring proxy.

**Q5. JDK proxy vs CGLIB?** Interface-based proxy versus subclass-based proxy.

## Practice
1. Log constructor, `@PostConstruct`, `afterPropertiesSet` and `@PreDestroy`.
2. Create an aspect that logs method entry and execution time.
3. Demonstrate external invocation versus self-invocation for a transactional method.
