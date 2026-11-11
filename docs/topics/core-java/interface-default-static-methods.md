# Default and Static Methods in Java Interfaces

## 1. Why were they introduced?

Before Java 8, interfaces mainly declared abstract methods and constants. Adding a new abstract method could break every existing implementation. Java 8 introduced default and static methods so interfaces could gain behavior while keeping many older implementations source-compatible.

## 2. Default methods

A default method has an implementation and uses the `default` keyword.

```java
interface Auditable {
    default void audit(String action) {
        System.out.println("AUDIT: " + action);
    }
}
class OrderService implements Auditable {}
```

The implementing class inherits the default method and may override it.

Use default methods for behavior closely related to the interface contract, not as a place to put unrelated utility code or large stateful workflows. Interfaces cannot hold per-instance state; their fields are implicitly public static final constants.

## 3. Static methods

A static interface method belongs to the interface itself. Call it using the interface name.

```java
interface IdValidator {
    static boolean isValid(String id) {
        return id != null && !id.isBlank();
    }
}
boolean valid = IdValidator.isValid("ORD-1");
```

A static interface method is not inherited as an instance method by implementing classes. Call it through the declaring interface.

## 4. Conflict resolution

If a class implements two interfaces that provide the same default method signature, the class must resolve the conflict.

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
        A.super.log(); // explicitly select A's implementation
    }
}
```

Rules to remember:
1. A class method wins over an interface default method.
2. A more specific subinterface default wins over a less specific parent-interface default.
3. If two unrelated interfaces conflict, the implementing class must override the method.

## 5. Calling a parent default

Use `InterfaceName.super.methodName()` from the implementing class to invoke a particular inherited default implementation. This syntax is useful when extending the default behavior.

## 6. Default method versus abstract class method

| Interface default method | Abstract class concrete method |
|---|---|
| A class can implement many interfaces | A class can extend only one class |
| No per-instance fields | Can hold instance state |
| Cannot use protected/package-private instance method visibility; interface methods are public | Supports normal class access modifiers |
| Best for optional contract behavior | Best for shared state and closely related base behavior |

## 7. Real project example

A common interface may provide a small default validation or metadata method shared by implementations. If implementations need shared configuration, injected collaborators, or mutable state, use a class/service instead.

## 8. Interview questions and answers

**Q1. Why did Java introduce default methods?**  
To evolve interfaces by adding behavior without forcing every existing implementation to implement a new abstract method.

**Q2. Can an interface have a static method?**  
Yes. It belongs to the interface and is called as InterfaceName.method(). It is not inherited as an instance method.

**Q3. What if two interfaces have the same default method?**  
The implementing class must override it and resolve the conflict, optionally calling one using InterfaceName.super.method().

**Q4. Which wins: a superclass method or an interface default?**  
The class implementation wins. The class hierarchy takes precedence over interface defaults.

**Q5. Can a default method access an instance field declared in the interface?**  
Interfaces cannot declare instance fields. They can access constants and methods, or state exposed by the implementing object through its contract.

**Q6. Can default methods be overridden?**  
Yes, an implementing class or a subinterface can override them.

## 9. Practice

Create two interfaces with the same default method, implement both in one class, resolve the conflict, and demonstrate calling one parent default explicitly.

## 10. Quick revision

default = inherited instance behavior; static = interface-owned utility behavior. Class methods win over defaults; unrelated interface conflicts must be resolved by the class.
