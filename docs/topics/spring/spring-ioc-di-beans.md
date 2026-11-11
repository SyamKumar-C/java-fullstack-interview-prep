# Spring IoC, Dependency Injection and Beans

Inversion of Control means the framework manages object creation and lifecycle. Dependency Injection supplies collaborators from outside rather than having a class construct them.

```java
@Service
class OrderService {
  private final PaymentGateway gateway;
  OrderService(PaymentGateway gateway) { this.gateway = gateway; }
}
```

Constructor injection makes dependencies explicit, supports immutability and simplifies testing.

Register beans with @Component, @Service, @Repository, @Controller, @RestController or @Bean methods inside @Configuration classes.

## Scopes
singleton (default, one per bean definition per container), prototype, request, session, application and websocket. A singleton service must be safe for concurrent requests; avoid request-specific mutable fields.

**Interview:** Is Spring singleton the same as GoF singleton? No. It is one instance per bean definition per container, not necessarily one per JVM.
