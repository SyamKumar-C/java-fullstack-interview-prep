# OOP Principles and SOLID Design in Java

## 1. What is object-oriented programming?

Object-oriented programming models software using objects. An object combines **state** (data) and **behavior** (methods); a class describes the structure and behavior of its instances.

OOP is useful when business concepts have identity, state, rules and behavior. It is not a goal to turn every piece of code into a class.

## 2. The four OOP principles

### Encapsulation

Encapsulation protects an object's state and ensures changes go through valid operations. It is more than making fields private and generating setters.

```java
class BankAccount {
    private BigDecimal balance = BigDecimal.ZERO;

    public void deposit(BigDecimal amount) {
        if (amount == null || amount.signum() <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        balance = balance.add(amount);
    }

    public BigDecimal getBalance() {
        return balance;
    }
}
```

The caller cannot directly set an invalid balance. The object protects its invariant.

### Abstraction

Abstraction exposes a useful contract and hides implementation details. Interfaces, abstract classes and public APIs can provide abstraction.

```java
interface PaymentGateway {
    PaymentResult charge(Money amount);
}
```

Checkout code can use the contract without knowing whether the implementation calls a bank, UPI provider or card processor.

### Inheritance

Inheritance creates a subtype using `extends`. Java allows a class to extend only one class, but it can implement multiple interfaces.

Use inheritance for a true **is-a** relationship. A subtype should be usable wherever its parent is expected without breaking the parent's promises (Liskov Substitution Principle).

Do not inherit only to reuse a few methods. That creates tight coupling to the parent implementation.

### Polymorphism

Polymorphism lets different implementations be used through the same parent or interface type.

```java
interface NotificationSender {
    void send(String message);
}
class EmailSender implements NotificationSender {
    public void send(String message) { System.out.println("Email: " + message); }
}
class SmsSender implements NotificationSender {
    public void send(String message) { System.out.println("SMS: " + message); }
}

NotificationSender sender = new EmailSender();
sender.send("Order placed"); // EmailSender implementation
sender = new SmsSender();
sender.send("Order placed"); // SmsSender implementation
```

Overloading is resolved at compile time. Overridden instance methods are dispatched at runtime based on the actual object.

## 3. Composition versus inheritance

Composition means an object uses or owns another object. Prefer it when the relationship is **has-a/uses-a**, or when behavior should be replaceable.

For example, `CheckoutService` has a `PaymentGateway`; it is not a payment gateway. Constructor injection makes the collaborator explicit and testable.

| Inheritance | Composition |
|---|---|
| Models an is-a relationship | Models has-a/uses-a |
| Child is coupled to parent behavior | Collaborator can be replaced |
| Useful for stable subtype relationships | Useful for flexible behavior and testing |

## 4. SOLID principles

These five design principles are frequent follow-up questions after OOP.

### S — Single Responsibility Principle (SRP)

A class should have one focused responsibility and one main reason to change.

Bad design: an `OrderService` validates orders, calculates tax, writes SQL, sends email and creates PDF invoices.

Better: keep order orchestration in the service and delegate tax calculation, persistence and notifications to focused collaborators. SRP does not mean every method needs its own class.

### O — Open/Closed Principle (OCP)

Software entities should be open for extension but closed for repeated modification. Add a new implementation behind a stable contract rather than adding another conditional branch everywhere.

For example, add `CardPaymentGateway` or `UpiPaymentGateway` behind `PaymentGateway`. The checkout workflow need not change.

### L — Liskov Substitution Principle (LSP)

A subtype must preserve the behavioral expectations of its parent type. It should not strengthen preconditions, weaken postconditions or break promised invariants.

Classic warning: a `Square` subtype of a mutable `Rectangle` can violate code that expects width and height to change independently. Prefer a different abstraction if the behaviors are not substitutable.

### I — Interface Segregation Principle (ISP)

Clients should not be forced to depend on methods they do not use. Prefer small role-based interfaces over one large interface containing unrelated operations.

For example, separate `Readable`, `Writable` and `Deletable` capabilities if some clients only need read access.

### D — Dependency Inversion Principle (DIP)

High-level business policy should depend on abstractions, not low-level implementation details. Both should depend on contracts.

A service depending on `PaymentGateway` rather than directly constructing a vendor SDK client follows DIP. Dependency injection is one way to provide the implementation; DIP is the design principle, DI is a technique.

## 5. OOP concepts that interviewers often distinguish

| Concept | Meaning |
|---|---|
| Class | Blueprint/type definition |
| Object | Runtime instance |
| State | Values held by an object |
| Behavior | Operations exposed by an object |
| Association | General relationship between objects |
| Aggregation | Whole-part relationship where parts can exist independently |
| Composition | Strong ownership/lifecycle relationship |
| Coupling | Degree of dependency between components |
| Cohesion | How closely a component's responsibilities belong together |

Aggregation and composition are design concepts; Java does not have separate language keywords for them.

## 6. Practical Spring Boot example

A controller calls an `OrderService`. The service depends on `PaymentGateway` and `OrderRepository` interfaces. Spring injects implementations through the constructor.

This design demonstrates abstraction, polymorphism, composition and dependency inversion. It also makes unit tests easier because tests can supply fake or mocked collaborators.

Do not create interfaces mechanically for every class. An interface is useful when it defines a meaningful boundary, multiple implementations, or a testing seam.

## 7. Common interview traps

- Abstraction is not the same as encapsulation.
- Getters and setters do not automatically guarantee encapsulation.
- Inheritance is not always the best form of code reuse.
- Overloading is not runtime polymorphism.
- A parent reference does not change the actual object's class.
- Dependency injection and dependency inversion are related but not identical.
- SOLID principles are design guidelines, not rigid rules to create more classes.

## 8. Interview questions and answers

**Q1. Explain the four pillars with an example.**  
Use a payment module: private payment state with validated operations is encapsulation; callers use a PaymentGateway contract without knowing provider details (abstraction); a subtype may specialize a base type (inheritance); Card and UPI implementations respond through the same interface (polymorphism).

**Q2. Abstraction versus encapsulation?**  
Abstraction focuses on what a caller can do while hiding implementation. Encapsulation protects internal state and controls how it changes.

**Q3. Why prefer composition over inheritance?**  
Composition reduces coupling to a parent class and allows behavior to be replaced independently. Use inheritance when a genuine substitutable subtype exists.

**Q4. Explain DIP versus dependency injection.**  
DIP says high-level policy should depend on abstractions. Dependency injection supplies an object's dependencies from outside rather than making the object create them itself.

**Q5. Give an LSP violation.**  
A subtype that throws an unsupported-operation exception for a method promised by its parent may violate substitutability. A Square that breaks Rectangle's independent width/height behavior is another common illustration.

**Q6. How does Spring support polymorphism?**  
A service can depend on an interface and receive one of several bean implementations. Use `@Qualifier` or `@Primary` when selection is ambiguous.

**Q7. Does SRP mean one method per class?**  
No. It means one cohesive responsibility or reason to change, not an arbitrary class-size limit.

## 9. Practice exercises

1. Design a notification module with email, SMS and push implementations. Identify abstraction and runtime polymorphism.
2. Refactor a service that validates orders, writes to a database and sends email. Explain SRP and composition.
3. Design a discount policy using an interface. Add a new policy without changing the checkout workflow; explain OCP.
4. Give an example where inheritance would violate LSP.
5. Split a large `FileOperations` interface into smaller role-based interfaces; explain ISP.
6. Make a service depend on a repository interface rather than directly creating a database client; explain DIP.

## 10. Quick revision

- Encapsulation: protect state and invariants.
- Abstraction: expose a useful contract.
- Inheritance: specialize a substitutable is-a type.
- Polymorphism: one contract, multiple implementations.
- Composition: build behavior from collaborators.
- SOLID: SRP, OCP, LSP, ISP, DIP.
