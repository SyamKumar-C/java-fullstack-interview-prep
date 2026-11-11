# Spring IoC, Dependency Injection and Beans

## 1. What problem does Spring solve?

A class should not need to create and manage every dependency it uses. If an OrderService creates its own PaymentClient, it becomes tightly coupled to that implementation and is harder to test or replace.

Spring's IoC container creates and manages objects (beans), connects their dependencies, and controls their lifecycle.

## 2. Inversion of Control (IoC)

Without IoC, application code controls object creation:

```java
class OrderService {
    private final PaymentClient client = new PaymentClient();
}
```

With IoC, the container creates and supplies the dependency:

```java
@Service
class OrderService {
    private final PaymentClient client;

    OrderService(PaymentClient client) {
        this.client = client;
    }
}
```

Control of object creation is inverted from the service to the container.

## 3. Dependency Injection (DI)

DI is a way to implement IoC: dependencies are provided from outside instead of being constructed inside the class.

Common forms:
- Constructor injection: dependencies supplied through the constructor.
- Setter injection: dependency supplied through a setter.
- Field injection: dependency injected into a field.

Constructor injection is generally preferred because it makes required dependencies explicit, supports immutability, and makes unit testing straightforward.

## 4. What is a Spring bean?

A bean is an object managed by the Spring IoC container. Beans can be registered using component scanning or configuration methods.

Component scanning:
```java
@Service
class OrderService {}
```

Java configuration:
```java
@Configuration
class AppConfig {
    @Bean
    PaymentClient paymentClient() {
        return new PaymentClient();
    }
}
```

Use `@Component` and its specializations such as `@Service`, `@Repository`, and `@Controller` for scanned classes. Use `@Bean` when you need explicit construction, often for third-party classes or custom setup.

## 5. Bean naming and injection

By default, a scanned bean name is commonly the class name with its first character lowercased. Explicit names can be provided.

When multiple beans implement the same interface, Spring needs a choice:

```java
@Service("upiPayment")
class UpiPayment implements PaymentClient {}

@Service("cardPayment")
class CardPayment implements PaymentClient {}

@Service
class CheckoutService {
    CheckoutService(@Qualifier("upiPayment") PaymentClient payment) {}
}
```

Other options include `@Primary` to mark a preferred bean. Use qualifiers when the choice should be explicit at an injection point.

## 6. Bean scopes

Common scopes:
- singleton (default): one bean instance per Spring IoC container.
- prototype: a new instance each time the container is asked for the bean.
- request: one instance per HTTP request in a web application.
- session: one instance per HTTP session.
- application: one instance per ServletContext.

Singleton does not mean one object for the entire JVM; it is one per bean definition/container. A singleton bean should generally be stateless or thread-safe because many requests can use it concurrently.

## 7. Circular dependencies

A circular dependency occurs when A requires B and B requires A. Constructor cycles are rejected because neither object can be constructed first. Redesign the responsibilities, introduce a third collaborator, or use events where appropriate. Do not use lazy injection simply to hide a poor dependency graph.

## 8. Testing benefit

Constructor injection makes unit tests simple:

```java
var service = new OrderService(fakePaymentClient);
```

The test can provide a fake or mock without starting the entire Spring context.

## 9. Common mistakes

- Using field injection everywhere.
- Treating singleton beans as automatically thread-safe.
- Adding `@Component` to every class without considering ownership.
- Ignoring multiple-bean ambiguity.
- Creating circular dependencies.
- Using application context lookups as a service locator in ordinary business code.

## 10. Interview questions and answers

**Q1. What is IoC?**  
A design principle where object creation and dependency management are handled by a container rather than by each application class.

**Q2. What is DI?**  
A technique for implementing IoC by supplying a class's dependencies from outside.

**Q3. Why is constructor injection preferred?**  
It makes required dependencies explicit, supports final fields, avoids partially initialized objects, and simplifies unit testing.

**Q4. Difference between @Component and @Bean?**  
@Component marks a class for component scanning. @Bean marks a method whose returned object should be managed by Spring.

**Q5. What happens if two beans match one interface?**  
Spring needs a clear candidate. Use @Qualifier at the injection point or @Primary for a default preference.

**Q6. Is a singleton bean thread-safe?**  
No. Scope controls instance count, not thread safety. Mutable shared state must be protected or avoided.

**Q7. Can Spring inject prototype into singleton?**  
It can, but ordinary injection resolves the prototype when the singleton is created, so the singleton holds that one instance. Use a provider or scoped proxy if a fresh instance is needed per access.

## 11. Practice exercises

1. Create two PaymentClient implementations and inject one using @Qualifier.
2. Write a unit test for OrderService using constructor injection and a fake client.
3. Explain why a singleton service with a mutable request-specific field is unsafe.

## 12. Quick revision

IoC = container controls object creation. DI = container supplies dependencies. Prefer constructor injection. Singleton scope does not imply thread safety.
