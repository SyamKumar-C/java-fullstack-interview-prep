# OOP Principles in Java

## 1. What is object-oriented programming?

Object-oriented programming (OOP) organizes software around objects. An object combines **state** (data) and **behavior** (methods). A class is the blueprint; an object is an instance created from that blueprint.

OOP helps model business concepts, protect data, reuse behavior, and change implementations without changing every caller.

```java
class BankAccount {
    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        balance += amount;
    }

    public double getBalance() { return balance; }
}
```

The balance is state. `deposit` is behavior. The private field prevents callers from changing the balance directly.

## 2. The four main principles

### Encapsulation
Keep data and the operations that protect it together. Hide internal state and expose controlled methods.

- Use private fields.
- Validate changes at the boundary.
- Avoid public setters when they allow invalid states.
- Example: an order should not allow its total to become negative.

### Abstraction
Expose what a caller needs and hide implementation details. Java uses interfaces and abstract classes to define contracts.

```java
interface PaymentGateway {
    void pay(long amountInPaise);
}
class UpiGateway implements PaymentGateway {
    public void pay(long amountInPaise) {
        System.out.println("Paying through UPI");
    }
}
```

The checkout service depends on the payment contract, not the UPI implementation.

### Inheritance
A subclass reuses and specializes behavior from a superclass using `extends`. Java supports single class inheritance, but a class can implement multiple interfaces.

Use inheritance only for a genuine **is-a** relationship and when the subtype can safely replace the parent (Liskov Substitution Principle). Prefer composition when you only want code reuse.

### Polymorphism
The same contract can have different implementations.

- Compile-time polymorphism: method overloading; compiler selects a signature.
- Runtime polymorphism: overridden instance method; actual object type determines the implementation.

```java
PaymentGateway gateway = new UpiGateway();
gateway.pay(25000); // UpiGateway implementation runs
```

## 3. Overloading versus overriding

| Point | Overloading | Overriding |
|---|---|---|
| Meaning | Same method name, different parameter list | Subclass replaces inherited instance behavior |
| Selection | Compile time | Runtime dispatch |
| Parameters | Must differ | Must match (or valid covariant return) |
| Return type alone | Cannot distinguish overloads | Must be same or covariant |
| Static methods | Can be overloaded | Hidden, not overridden |

## 4. Real project scenario

A notification module may define `NotificationSender` with `send(message)`. Email, SMS, and push implementations satisfy the same contract. The business service accepts the interface through constructor injection. Adding WhatsApp support then does not require rewriting the business workflow.

Do not create an interface for every class automatically. Use one when you need multiple implementations, a stable boundary, or a useful testing seam.

## 5. Common mistakes

- Treating inheritance as the default way to share code.
- Exposing mutable fields and calling it encapsulation.
- Confusing abstraction (what is exposed) with encapsulation (how state is protected).
- Assuming a parent reference changes the object's runtime type.
- Creating deep inheritance trees that are difficult to test.

## 6. Interview questions and answers

**Q1. Explain the four OOP pillars with one example.**  
Use a bank account: private balance plus validated deposit is encapsulation; callers use deposit without knowing storage details (abstraction); SavingsAccount may extend Account only if it is truly an Account (inheritance); different Account implementations can respond differently through the same method (polymorphism).

**Q2. Is abstraction possible without an interface?**  
Yes. Abstract classes and well-designed public APIs can also expose a simpler contract while hiding details.

**Q3. Why prefer composition over inheritance?**  
Composition keeps components replaceable and avoids tight coupling to a parent implementation. A service can contain a PaymentGateway rather than inherit from a particular gateway.

**Q4. Can we achieve runtime polymorphism with fields?**  
No. Field access is resolved using the reference's declared type; overridden instance methods use runtime dispatch.

**Q5. Give an example of polymorphism in Spring.**  
A service depends on an interface such as PaymentProcessor. Spring injects one implementation based on configuration or qualifiers.

## 7. Practice exercise

Design a discount module with Regular, Premium, and Employee discount policies. Define a common contract, implement each policy, and make the checkout service depend only on that contract. Add a test for each policy.

## 8. Quick revision

- Encapsulation: protect state.
- Abstraction: expose a useful contract.
- Inheritance: specialize an is-a relationship.
- Polymorphism: one contract, multiple behaviors.
