# Abstract Classes vs Interfaces

## 1. Abstract class

An abstract class cannot be instantiated directly. It can contain instance state, constructors, concrete methods and abstract methods.

```java
abstract class Payment {
    private final String reference;

    Payment(String reference) {
        this.reference = reference;
    }

    String reference() { return reference; }
    abstract void pay();
}
```

A subclass must implement inherited abstract methods unless it is also abstract.

## 2. Interface

An interface defines a contract that classes can implement. A class can implement multiple interfaces.

```java
interface Refundable {
    void refund();
    default boolean supportsPartialRefund() { return false; }
    static boolean isValidAmount(double amount) { return amount > 0; }
}
```

Interface fields are implicitly `public static final`. Abstract interface methods are implicitly public. Interfaces can also contain default, static and private helper methods (private interface methods were added in Java 9).

## 3. Comparison

| Feature | Abstract class | Interface |
|---|---|---|
| Multiple inheritance | A class extends one class | A class can implement many |
| Instance fields | Yes | No ordinary instance fields |
| Constructor | Yes | No |
| State | Can hold per-object state | Constants only |
| Methods | Abstract and concrete | Abstract, default, static, private |
| Best fit | Shared base identity/state | Capability or contract |

## 4. Choosing between them

Use an abstract class when related types share state, constructor logic or protected implementation. Use an interface when different types need to promise the same capability without sharing a base-class identity.

Example: `CardPayment` and `UpiPayment` may extend a shared `Payment` base if they share payment state, while both can implement `Refundable`.

## 5. Default method conflicts

If two unrelated interfaces provide the same default method, the implementing class must resolve the conflict.

```java
interface A { default void log() { System.out.println("A"); } }
interface B { default void log() { System.out.println("B"); } }
class C implements A, B {
    @Override public void log() {
        A.super.log();
    }
}
```

A class method takes precedence over an interface default method. A more specific interface can also take precedence over a less specific parent interface.

## 6. Interview questions

**Can an abstract class have a constructor?** Yes. It runs as part of subclass construction.

**Can an interface extend another interface?** Yes, and it can extend multiple interfaces.

**Can an abstract class implement an interface without implementing every method?** Yes, if the abstract class remains abstract.

**Can an interface have a private method?** Yes, since Java 9; it helps share code between its own default or static methods.

## 7. Practice

Design a payment model with an abstract base class and separate interfaces for refund, audit and notification capabilities. Explain why each responsibility belongs where you placed it.
