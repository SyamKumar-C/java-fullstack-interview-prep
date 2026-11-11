# Method Overloading vs Method Overriding

## 1. Overloading

Overloading means declaring methods with the same name but different parameter lists in the same class (or inherited method context). It is resolved at compile time using the declared types and arguments.

```java
class Calculator {
    int add(int a, int b) { return a + b; }
    long add(long a, long b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

Changing only the return type is not enough:

```java
// Invalid: same name and same parameter list
int getValue() { return 1; }
// double getValue() { return 1.0; }
```

The compiler chooses the most specific applicable overload. Widening, boxing and varargs are considered in defined phases; ambiguous calls fail compilation.

## 2. Overriding

Overriding means a subclass provides an implementation of an inherited instance method with a compatible signature. The selected implementation is based on the actual runtime object (dynamic dispatch).

```java
class Notification {
    void send() { System.out.println("Generic notification"); }
}
class EmailNotification extends Notification {
    @Override
    void send() { System.out.println("Email sent"); }
}
Notification n = new EmailNotification();
n.send(); // Email sent
```

Use `@Override`: it asks the compiler to verify that the method really overrides a parent method.

## 3. Rules to remember

- An overriding method cannot reduce visibility.
- It may return the same type or a covariant (more specific reference) return type.
- It cannot add broader checked exceptions than the parent method allows.
- `final` methods cannot be overridden.
- `private` methods are not overridden because they are not inherited.
- Static methods are hidden, not overridden.
- Fields are hidden, not dynamically dispatched.
- Constructors are not overridden.

## 4. Overloading and runtime polymorphism together

```java
class Printer {
    void print(Object value) { System.out.println("Object"); }
    void print(String value) { System.out.println("String"); }
}
Object value = "hello";
new Printer().print(value); // Object
```

The compiler sees the declared type `Object`, so it selects `print(Object)`. Overload selection happens at compile time; overriding selection happens at runtime.

## 5. Quick comparison

| Point | Overloading | Overriding |
|---|---|---|
| Parameters | Must differ | Same signature |
| Typical relationship | Same class | Parent and child |
| Binding | Compile time | Runtime for instance methods |
| Return type | Cannot distinguish overloads | Same or covariant |
| Static methods | Can be overloaded | Hidden, not overridden |

## 6. Interview questions

**Can we overload main()?** Yes, but the JVM entry point remains `public static void main(String[] args)`.

**Can static methods be overridden?** No. A same-signature static method in a subclass hides the parent method.

**Can we override a method and make it private?** No; that reduces visibility.

**Why does the Object example print Object?** Overload resolution uses the variable's declared type, not the object's runtime class.

## 7. Practice

Predict the output when a parent reference points to a child object and both classes overload and override methods. Explain separately which decision is compile-time and which is runtime.
