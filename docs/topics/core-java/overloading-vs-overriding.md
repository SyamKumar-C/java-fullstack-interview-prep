# Method Overloading vs Method Overriding

## 1. Overloading

Overloading declares methods with the same name but different parameter lists. The compiler selects the overload using declared argument types and applicable conversion rules.

```java
class Calculator {
    int add(int a, int b) { return a + b; }
    long add(long a, long b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

Return type alone cannot distinguish overloads. Ambiguous calls fail at compile time. Overload resolution considers widening, boxing and varargs in defined phases.

## 2. Overriding

A subclass overrides an inherited instance method with a compatible signature. The implementation is selected at runtime based on the actual object.

```java
class Notification {
    void send() { System.out.println("Generic"); }
}
class EmailNotification extends Notification {
    @Override void send() { System.out.println("Email"); }
}
Notification n = new EmailNotification();
n.send(); // Email
```

Use `@Override` so the compiler checks that the method really overrides a parent method.

## 3. Rules

- Overriding cannot reduce visibility.
- Return type must be same or covariant.
- Checked exceptions cannot be broader than those allowed by the parent method.
- final methods cannot be overridden.
- private methods are not overridden.
- Static methods are hidden, not overridden.
- Fields are hidden, not dynamically dispatched.
- Constructors are not overridden.

## 4. Overloading plus runtime dispatch

```java
class Printer {
    void print(Object value) { System.out.println("Object"); }
    void print(String value) { System.out.println("String"); }
}
Object value = "hello";
new Printer().print(value); // Object
```

The compiler sees the declared type Object and selects print(Object). Runtime type does not change overload selection.

## 5. Comparison

| Point | Overloading | Overriding |
|---|---|---|
| Parameters | Different | Same signature |
| Typical location | Same class | Parent and child |
| Binding | Compile time | Runtime for instance methods |
| Return type | Cannot distinguish overloads | Same/covariant |
| Static method | Can be overloaded | Hidden, not overridden |

## 6. Interview questions

**Can main be overloaded?** Yes, but the standard JVM entry point remains main(String[]).

**Can static methods be overridden?** No, they are hidden.

**Can an override be private?** No, that reduces visibility.

**Why does the Object example print Object?** Overload resolution uses the declared reference type.

## 7. Practice

Create parent and child classes with overloaded and overridden methods. Predict output and identify each compile-time and runtime decision.
