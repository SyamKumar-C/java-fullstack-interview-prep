# Object-Oriented Programming (OOP)

> **Priority:** P1  
> **Level:** Beginner to intermediate  
> **Estimated study time:** 35–45 minutes

## 1. Short interview answer

Object-oriented programming is a way of designing software around objects. An object combines **state** (data) and **behavior** (methods). Java uses classes to define objects and supports four commonly discussed OOP principles: encapsulation, abstraction, inheritance, and polymorphism.

## 2. Concept and purpose

OOP helps organize code around the things and responsibilities in a business domain.

For example, an order application may have **Customer**, **Order**, and **Payment** classes. Each class owns related data and behavior.

- **Encapsulation:** Keep data and the methods that operate on it together, and control access to the internal state.
- **Abstraction:** Expose what a caller needs while hiding implementation details.
- **Inheritance:** Create a new class based on an existing class to reuse or specialize behavior.
- **Polymorphism:** Use a common parent type or interface while allowing different implementations to behave differently.

## 3. How it works

### Encapsulation

A class protects its state by restricting direct access and exposing controlled methods.

```java
public class BankAccount {
    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

The caller cannot directly assign to **balance**. It must use **deposit()**, where validation is applied.

### Abstraction

An interface describes a capability without exposing the implementation.

```java
interface PaymentService {
    void pay(double amount);
}

class CardPaymentService implements PaymentService {
    @Override
    public void pay(double amount) {
        System.out.println("Paid by card: " + amount);
    }
}
```

The calling code can depend on **PaymentService**, rather than knowing the card-payment details.

### Inheritance

A child class inherits accessible members from a parent class.

```java
class Employee {
    void work() {
        System.out.println("Employee is working");
    }
}

class Developer extends Employee {
    void writeCode() {
        System.out.println("Writing code");
    }
}
```

**Developer** can use **work()** and add its own **writeCode()** behavior.

### Polymorphism

A parent reference can point to an object of a child implementation. The overridden method is selected at runtime.

```java
interface Notification {
    void send(String message);
}

class EmailNotification implements Notification {
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

class SmsNotification implements Notification {
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

public class Demo {
    public static void main(String[] args) {
        Notification notification = new EmailNotification();
        notification.send("Interview reminder");

        notification = new SmsNotification();
        notification.send("Interview reminder");
    }
}
```

The same method call, **send()**, produces different behavior depending on the actual object.

## 4. Practical use in a Spring Boot application

A service can depend on an interface:

```java
public interface PaymentGateway {
    void charge(long amountInPaise);
}

@Service
class UpiPaymentGateway implements PaymentGateway {
    @Override
    public void charge(long amountInPaise) {
        // Call the UPI provider
    }
}

@Service
class CheckoutService {
    private final PaymentGateway paymentGateway;

    CheckoutService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    void checkout() {
        paymentGateway.charge(15000);
    }
}
```

This uses abstraction and polymorphism. Spring injects an implementation of the interface. If multiple implementations exist, use a clear choice such as **@Qualifier** or **@Primary**.

## 5. Comparison and trade-offs

| Principle | Main purpose | Example |
|---|---|---|
| Encapsulation | Protect and control state | Private balance with deposit method |
| Abstraction | Hide implementation details | PaymentGateway interface |
| Inheritance | Reuse or specialize a type | Developer extends Employee |
| Polymorphism | Vary behavior behind a common type | Different payment implementations |

Prefer composition over inheritance when a class only needs to use another object's behavior and is not truly a specialized form of that type. Deep inheritance chains can make code harder to change.

## 6. Common mistakes

- **Calling getters and setters encapsulation by itself:** Encapsulation is about controlling access and protecting valid state, not simply generating getters and setters.
- **Confusing abstraction with encapsulation:** Abstraction hides implementation complexity; encapsulation protects and controls internal state.
- **Using inheritance only for code reuse:** Inheritance should represent a genuine “is-a” relationship.
- **Assuming overloading is runtime polymorphism:** Method overloading is resolved at compile time; overridden instance methods use runtime dispatch.

## 7. Interview questions

??? question "Q1. What are the four pillars of OOP?"
    **Answer:** Encapsulation, abstraction, inheritance, and polymorphism. Encapsulation controls access to state, abstraction hides implementation details, inheritance specializes an existing type, and polymorphism allows different implementations to be used through a common type.

??? question "Q2. What is the difference between abstraction and encapsulation?"
    **Answer:** Abstraction focuses on what an object exposes and hides unnecessary implementation details. Encapsulation groups state and behavior and controls access to the object's internal state.

??? question "Q3. What is runtime polymorphism in Java?"
    **Answer:** Runtime polymorphism happens when an overridden instance method is called through a parent-class or interface reference. The JVM selects the implementation based on the actual object.

??? question "Q4. Why should we prefer composition over inheritance in some cases?"
    **Answer:** Composition keeps components loosely coupled and lets behavior be changed by replacing a collaborator. Inheritance creates a tighter relationship and can become difficult to maintain in deep class hierarchies.

??? question "Q5. Does Java support multiple inheritance?"
    **Answer:** Java does not allow a class to extend multiple classes. A class can implement multiple interfaces, which allows it to inherit multiple type contracts. Interfaces can also provide default methods, with conflict rules when defaults clash.

## 8. Quick revision

- **Remember:** Encapsulation protects state; abstraction hides details; inheritance specializes; polymorphism varies behavior.
- **Common follow-up:** Compile-time vs runtime polymorphism.
- **One-line takeaway:** Program to a useful abstraction, keep state valid, and use inheritance only for a real type relationship.

## 9. Practice

1. Create a **BankAccount** class that prevents negative deposits and withdrawals.
2. Create a **ReportExporter** interface with PDF and CSV implementations.
3. Explain why **CheckoutService** should depend on **PaymentGateway** instead of directly creating a concrete payment class.
