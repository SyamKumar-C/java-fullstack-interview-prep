# Spring IoC, Dependency Injection and Beans

## 1. Short interview answer
IoC means the framework controls object creation and wiring instead of application classes creating their own dependencies. Dependency Injection is the technique Spring uses to provide those dependencies. A Spring bean is an object managed by the IoC container.

## 2. Why IoC matters
Without DI, a service constructs its own collaborators and becomes tied to concrete implementations:

```java
class OrderService {
    private final PaymentClient client = new StripePaymentClient();
}
```

With constructor injection, the dependency is supplied from outside:

```java
@Service
class OrderService {
    private final PaymentClient client;
    OrderService(PaymentClient client) { this.client = client; }
}
```

Now a test can pass a fake client, and production configuration can choose another implementation. This is dependency inversion in practice; DI is the mechanism, not the same concept.

## 3. What happens during application startup?
1. Spring creates an ApplicationContext.
2. It reads configuration classes and scans configured packages.
3. It registers BeanDefinitions (metadata describing how to create beans).
4. It creates eager singleton beans, resolves dependencies and applies post-processors.
5. It publishes lifecycle events and completes context refresh.
6. The application uses beans through the container.

Not every bean is necessarily created at startup: lazy beans and prototype beans have different creation timing.

## 4. Registering beans

Component scanning:
```java
@Component
class TaxCalculator {}

@Service
class OrderService {}

@Repository
interface OrderRepository extends JpaRepository<Order, Long> {}
```

Java configuration:
```java
@Configuration
class PaymentConfiguration {
    @Bean
    PaymentClient paymentClient(PaymentProperties properties) {
        return new HttpPaymentClient(properties.baseUrl());
    }
}
```

Use stereotypes for application classes discovered by scanning. Use `@Bean` when construction needs explicit configuration or the type comes from a third-party library.

## 5. Constructor, setter and field injection

| Type | Good fit | Trade-off |
|---|---|---|
| Constructor | Required dependencies | Clear, testable, supports final fields |
| Setter | Optional/reconfigurable dependency | Object can exist before optional dependency is set |
| Field | Rarely recommended | Hidden dependency; harder plain unit testing |

A single constructor does not normally need `@Autowired`. Avoid field injection for required collaborators.

## 6. Multiple implementations: @Primary and @Qualifier
```java
interface PaymentGateway { void pay(); }

@Component("upiGateway")
class UpiGateway implements PaymentGateway {
    public void pay() {}
}

@Component("cardGateway")
class CardGateway implements PaymentGateway {
    public void pay() {}
}

@Service
class CheckoutService {
    CheckoutService(@Qualifier("upiGateway") PaymentGateway gateway) {}
}
```

`@Primary` marks a preferred candidate when injection is ambiguous. `@Qualifier` selects a named/qualified candidate at a particular injection point. If no unique candidate can be selected, context startup fails.

## 7. Bean scopes

- **singleton (default):** one instance per bean definition per ApplicationContext.
- **prototype:** a new instance each time the container resolves the bean.
- **request/session:** one instance per HTTP request/session in a web application.
- **application/websocket:** web-context-specific scopes.

Singleton does not mean one instance across every JVM or server. Singleton services are shared by concurrent requests, so avoid request-specific mutable fields.

A prototype injected directly into a singleton is normally resolved once when the singleton is created. To obtain a fresh instance for each use, use `ObjectProvider<T>`, a provider, or an appropriate scoped proxy.

## 8. Circular dependencies
If A requires B and B requires A, constructor injection cannot construct either first and Spring reports a cycle. Prefer redesigning responsibilities, extracting a third service, or publishing an application event. Lazy injection may defer creation but usually hides the design problem.

## 9. BeanFactory vs ApplicationContext
BeanFactory is the basic container contract. ApplicationContext builds on it and adds application events, internationalization, resource loading and integration with framework infrastructure. Most Spring Boot applications use an ApplicationContext.

## 10. Common interview traps
- A bean is not automatically thread-safe because it is singleton.
- DI and IoC are related but not synonyms: DI is one way to achieve IoC.
- `@Component` marks a class for scanning; `@Bean` marks a factory method.
- Prototype scope does not mean Spring automatically manages every destruction callback.
- Constructor injection exposes dependency cycles instead of silently masking them.

## 11. Interview questions
**Q1. What is IoC?** The framework/container takes control of creating and managing application objects.

**Q2. What is DI?** Supplying an object's dependencies from outside rather than constructing them inside it.

**Q3. Why constructor injection?** Required dependencies are explicit, fields can be final, invalid objects are harder to construct, and unit tests can instantiate the class directly.

**Q4. @Component vs @Bean?** Component scanning discovers annotated classes; a @Bean method explicitly returns an object to register.

**Q5. @Primary vs @Qualifier?** Primary is a default preference; Qualifier narrows the candidate at an injection point.

**Q6. Is a singleton bean thread-safe?** No. Scope defines lifecycle/instance count, not synchronization.

**Q7. How do you inject a fresh prototype into a singleton?** Use ObjectProvider or a provider to request it when needed.

## 12. Practice
1. Create two implementations of a payment interface and select one with `@Qualifier`.
2. Unit-test a service by passing a fake dependency to its constructor.
3. Demonstrate a prototype bean injected into a singleton and then use ObjectProvider to request new instances.
4. Explain why storing the current user's ID in a singleton service field is unsafe.

## Quick revision
IoC = container controls creation. DI = dependencies supplied from outside. Prefer constructor injection. Singleton is per container and is not automatically thread-safe.
