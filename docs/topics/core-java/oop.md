# Object-Oriented Programming (OOP)

## 1. Interview answer

Object-oriented programming organizes software around objects that combine state (data) and behavior (methods). Java uses classes to define objects and commonly teaches four principles: encapsulation, abstraction, inheritance and polymorphism.

## 2. Encapsulation

Encapsulation controls access to internal state and protects object invariants. It is more than generating getters and setters.

```java
class BankAccount {
    private BigDecimal balance = BigDecimal.ZERO;

    public void deposit(BigDecimal amount) {
        if (amount.signum() <= 0) throw new IllegalArgumentException();
        balance = balance.add(amount);
    }
    public BigDecimal balance() { return balance; }
}
```

The class ensures that callers cannot set an invalid balance directly.

## 3. Abstraction

Abstraction exposes what a caller needs and hides implementation details.

```java
interface PaymentGateway {
    void charge(BigDecimal amount);
}
```

Checkout code can use the contract without knowing how UPI, card or another provider works.

## 4. Inheritance

Inheritance creates a subtype from an existing class using `extends`. The child inherits accessible members and can specialize behavior. Java classes have single inheritance.

```java
class Employee { void work() {} }
class Developer extends Employee {
    void writeCode() {}
}
```

Use inheritance for a genuine “is-a” relationship, not merely to reuse a few lines.

## 5. Polymorphism

A parent/interface reference can refer to different implementation objects. Overridden instance methods are selected at runtime.

```java
interface Notification { void send(String message); }
class EmailNotification implements Notification {
    public void send(String m) { System.out.println("Email " + m); }
}
class SmsNotification implements Notification {
    public void send(String m) { System.out.println("SMS " + m); }
}
Notification notification = new EmailNotification();
notification.send("Hello");
notification = new SmsNotification();
notification.send("Hello");
```

Overloading is compile-time resolution; overriding is runtime dispatch.

## 6. Composition vs inheritance

Composition means one object contains or uses another object. It often gives more flexible designs because behavior can be replaced through a collaborator.

Example: CheckoutService has a PaymentGateway. CheckoutService is not a PaymentGateway; it uses one.

## 7. Practical Spring Boot example

A service depends on a PaymentGateway interface and Spring injects a concrete implementation. This uses abstraction, polymorphism and dependency injection. If multiple implementations exist, choose one with @Qualifier or @Primary.

## 8. Comparison

| Principle | Purpose | Example |
|---|---|---|
| Encapsulation | Protect state | Validated deposit method |
| Abstraction | Hide implementation | PaymentGateway |
| Inheritance | Specialize a type | Developer extends Employee |
| Polymorphism | Vary behavior | Email/SMS implementation |

## 9. Common mistakes

- Confusing abstraction with encapsulation.
- Thinking getters/setters alone guarantee encapsulation.
- Using inheritance only for code reuse.
- Calling overloading runtime polymorphism.
- Exposing mutable internal collections directly.

## 10. Interview questions

**What are the four pillars?** Encapsulation, abstraction, inheritance and polymorphism.

**Abstraction vs encapsulation?** Abstraction hides implementation details; encapsulation controls access to internal state.

**What is runtime polymorphism?** An overridden method call resolved using the actual object at runtime.

**Does Java support multiple inheritance?** Not for classes; a class can implement multiple interfaces.

**Why composition over inheritance?** It reduces tight coupling and allows collaborators to be replaced more easily.

## 11. Practice

Design a checkout flow with PaymentGateway implementations for card and UPI. Explain which OOP principles are used and why CheckoutService should depend on the interface.
