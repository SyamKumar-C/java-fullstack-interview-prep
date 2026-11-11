# Default, Static and Private Methods in Interfaces

## 1. Why were default methods introduced?

Before Java 8, adding a new abstract method to a widely implemented interface could break existing implementations. A default method adds behavior while keeping older implementations source-compatible in many cases.

```java
interface Auditable {
    default void audit() {
        System.out.println("Audit event");
    }
}
class OrderService implements Auditable { }
new OrderService().audit();
```

## 2. Default methods

- Declared with the `default` keyword and a method body.
- Inherited by implementing classes.
- Can be overridden by the implementing class.
- Are public; do not write a weaker access modifier.
- Support interface evolution, but should not become a dumping ground for unrelated business logic.

## 3. Static methods

Static interface methods belong to the interface itself. Call them using the interface name.

```java
interface IdValidator {
    static boolean valid(String id) {
        return id != null && !id.isBlank();
    }
}
boolean ok = IdValidator.valid("ORD-1");
```

They are not inherited as instance methods and are not overridden. A class does not call them through an object.

## 4. Private methods (Java 9+)

Private interface methods help reuse code inside default or static methods.

```java
interface Auditable {
    default void auditCreate() { write("CREATE"); }
    default void auditDelete() { write("DELETE"); }

    private void write(String action) {
        System.out.println("Audit: " + action);
    }
}
```

They are implementation helpers, not part of the public contract.

## 5. Resolving default method conflicts

If a class inherits matching defaults from two unrelated interfaces, it must override the method and choose or combine behavior.

```java
interface A { default String name() { return "A"; } }
interface B { default String name() { return "B"; } }
class C implements A, B {
    @Override public String name() {
        return A.super.name() + B.super.name();
    }
}
```

If a superclass provides a concrete method, the class method wins over an interface default.

## 6. Interview questions

**Are default methods the same as JavaScript default parameters?** No. A Java interface default method provides an inherited method implementation.

**Can a static interface method be overridden?** No. Static methods are associated with the declaring interface.

**Can a default method be private?** No. A default method is public; use a separate private helper method.

**Can an interface have a constructor?** No. Interfaces are not instantiated.

## 7. Practice

Create two interfaces with the same default method. Implement both and resolve the conflict using `A.super.method()`.
