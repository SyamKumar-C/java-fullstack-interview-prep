# OOP Principles: Encapsulation, Abstraction, Inheritance and Polymorphism

## 1. Encapsulation

Encapsulation keeps data and the operations that protect it together. It prevents callers from putting an object into an invalid state.

```java
class BankAccount {
    private long balance;

    public void deposit(long amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        balance += amount;
    }

    public long getBalance() { return balance; }
}
```

The key is not merely private fields and getters/setters. It is controlling how state changes.

## 2. Abstraction

Abstraction exposes what a caller needs and hides implementation details.

```java
interface PaymentGateway {
    void pay(long amount);
}
class CheckoutService {
    private final PaymentGateway gateway;
    CheckoutService(PaymentGateway gateway) { this.gateway = gateway; }
    void checkout() { gateway.pay(500); }
}
```

CheckoutService depends on the contract, not a specific payment provider. A different implementation can be supplied without changing the service.

## 3. Inheritance

Inheritance models an "is-a" relationship and reuses or specializes behavior.

```java
class Vehicle { void start() { System.out.println("Start"); } }
class Car extends Vehicle { void openBoot() { System.out.println("Boot open"); } }
```

Use inheritance only when the subtype can safely stand in for the parent (Liskov substitution). Do not use it only to reuse a few lines of code; composition is often more flexible.

## 4. Polymorphism

Polymorphism lets code use a common parent type or interface while runtime objects provide different behavior.

```java
interface Payment {
    void pay();
}
class CardPayment implements Payment {
    public void pay() { System.out.println("Card"); }
}
class UpiPayment implements Payment {
    public void pay() { System.out.println("UPI"); }
}
void process(Payment payment) {
    payment.pay();
}
```

The call to an overridden instance method is dispatched using the runtime object's class.

## 5. Composition vs inheritance

- Inheritance: "is-a". A Car is a Vehicle.
- Composition: "has-a". An OrderService has a PaymentGateway.

Composition allows behavior to be replaced by supplying another collaborator. Spring dependency injection commonly uses composition.

## 6. Interview questions

**Is encapsulation the same as data hiding?** Data hiding is one part; encapsulation also groups state with behavior and enforces valid operations.

**Why prefer composition?** It reduces tight coupling to a class hierarchy and makes behavior easier to replace and test.

**What is runtime polymorphism?** An overridden instance method is selected based on the actual object at runtime.

**Can constructors be polymorphic?** No. Constructors are not overridden.

## 7. Practice

Model notifications using an interface and email/SMS implementations. Then explain how a service can use the interface without knowing the concrete implementation.
