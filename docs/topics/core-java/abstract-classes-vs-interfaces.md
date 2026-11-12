# Abstract Classes vs Interfaces

## 1. Abstract class

An abstract class cannot be instantiated directly. It can contain instance state, constructors, concrete methods, abstract methods and methods with different access levels.

```java
abstract class Payment {
    private final String reference;

    protected Payment(String reference) {
        this.reference = reference;
    }

    String reference() { return reference; }
    abstract void pay();
}
```

A concrete subclass must implement all inherited abstract methods. An abstract subclass may leave some unimplemented.

## 2. Interface

An interface defines a contract/capability. A class can implement multiple interfaces, and an interface can extend multiple interfaces.

```java
interface Refundable {
    void refund();

    default boolean supportsPartialRefund() {
        return false;
    }

    static boolean isValidAmount(BigDecimal amount) {
        return amount != null && amount.signum() > 0;
    }

    private void auditHelper() {
        // helper shared by interface default/static methods
    }
}
```

Interface fields are implicitly `public static final`. Abstract interface methods are implicitly public. Interfaces can contain:

- Abstract methods.
- Default instance methods (Java 8).
- Static methods (Java 8).
- Private helper methods (Java 9).

An interface has no constructor and cannot hold ordinary per-instance fields.

## 3. Detailed comparison

| Feature | Abstract class | Interface |
|---|---|---|
| Class inheritance | A class can extend one class | A class can implement multiple interfaces |
| State | Instance fields allowed | No ordinary instance fields |
| Constructor | Yes | No |
| Abstract methods | Yes | Yes |
| Concrete methods | Yes, any suitable access | Default/static/private methods |
| Method fields | Instance and static | Fields are public static final constants |
| Access control | public/protected/package/private | Abstract methods are public; private helpers allowed |
| Typical purpose | Shared base state/implementation | Contract or capability |

## 4. When should you choose each?

Choose an abstract class when related types share meaningful state, constructor logic or protected implementation and the inheritance relationship is stable.

Choose an interface when different types need to promise the same capability but do not need a common base class or shared instance state.

Example: CardPayment and UpiPayment might extend a shared Payment base if they share payment state, while both implement Refundable and Auditable capabilities.

Do not create an abstract class just to prevent instantiation; a private constructor or factory may be better depending on the design.

## 5. Default method conflict resolution

If a class inherits two unrelated interface defaults with the same signature, it must override and resolve the conflict.

```java
interface A {
    default void log() { System.out.println("A"); }
}
interface B {
    default void log() { System.out.println("B"); }
}
class C implements A, B {
    @Override
    public void log() {
        A.super.log();
    }
}
```

Resolution rules to remember:

1. A concrete class method takes precedence over an interface default.
2. A more specific subinterface default takes precedence over a less specific parent interface default.
3. If two unrelated interfaces provide the same default, the class must resolve it explicitly.

## 6. Interface evolution and compatibility

Default methods allow an interface to gain behavior without forcing every existing implementation to add a method immediately. This was an important Java 8 change. It does not mean default methods should be used to put large shared stateful workflows into interfaces.

Static interface methods belong to the interface and are called using the interface name; they are not inherited as instance methods by implementing classes.

## 7. Common mistakes

- Saying interfaces cannot have implemented methods (default/static/private methods exist).
- Saying abstract classes cannot have constructors.
- Assuming interface fields are instance fields.
- Using an interface when shared mutable state is central to the abstraction.
- Using an abstract class where multiple unrelated types need the same capability.
- Forgetting to resolve unrelated default method conflicts.

## 8. Interview questions and answers

**Q1. Can an abstract class have a constructor?**  
Yes. It runs as part of constructing a concrete subclass and initializes the shared base state.

**Q2. Can an interface extend another interface?**  
Yes. It can extend multiple interfaces.

**Q3. Can an abstract class implement an interface without implementing all methods?**  
Yes, if the abstract class remains abstract.

**Q4. Can an interface have private methods?**  
Yes, from Java 9. They are helper methods used by the interface's own default or static methods.

**Q5. What happens if two interfaces have the same default method?**  
The implementing class must override it and select or combine the behavior.

**Q6. Can a class extend two abstract classes?**  
No. Java supports single class inheritance.

**Q7. Are interface variables constants?**  
Yes. They are implicitly public, static and final.

## 9. Practice

Design a payment model with an abstract base class and separate interfaces for refund, audit and notification capabilities. Add two payment implementations and explain which shared behavior belongs in the base class versus each capability interface.
