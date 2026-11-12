# Method Overloading vs Method Overriding

## 1. Overloading

Overloading declares methods with the same name but different parameter lists. The compiler selects the applicable method using the **declared types** of the arguments and Java's overload-resolution rules.

```java
class Calculator {
    int add(int a, int b) { return a + b; }
    long add(long a, long b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

Changing only the return type does not create a valid overload. Parameter count, types or order must differ.

### Overload resolution points

The compiler considers applicable methods in phases, broadly preferring strict invocation (identity/widening) before boxing/unboxing and then variable arity. If no most-specific method can be selected, the call is ambiguous and compilation fails.

```java
void show(long value) { System.out.println("long"); }
void show(Integer value) { System.out.println("Integer"); }

show(10); // long: primitive widening is preferred over boxing
```

Do not memorize isolated output examples without understanding the conversion rules.

## 2. Overriding

A subclass overrides an inherited instance method with a compatible signature. The selected implementation depends on the actual object at runtime.

```java
class Notification {
    void send() { System.out.println("Generic"); }
}
class EmailNotification extends Notification {
    @Override
    void send() { System.out.println("Email"); }
}

Notification notification = new EmailNotification();
notification.send(); // Email
```

Always use `@Override`; it lets the compiler catch a misspelled method or incompatible signature.

## 3. Overriding rules

- The method must be inherited and have a matching signature (after generic substitution/erasure rules where relevant).
- Visibility cannot be reduced.
- Return type must be identical or covariant (a subtype reference return).
- An overriding method cannot add broader checked exceptions than the overridden method allows.
- A final method cannot be overridden.
- A private method is not inherited and therefore is not overridden.
- Static methods are hidden, not overridden.
- Fields are hidden, not dynamically dispatched.
- Constructors are not overridden.
- An abstract method must be implemented by a concrete class unless an inherited implementation already satisfies it.

## 4. Overloading and runtime dispatch can happen together

```java
class Printer {
    void print(Object value) { System.out.println("Object overload"); }
    void print(String value) { System.out.println("String overload"); }
}
Object value = "hello";
new Printer().print(value); // Object overload
```

The compiler selects `print(Object)` from the declared type. Runtime dispatch then chooses an override of that selected signature, if one exists. Runtime type does not re-run overload resolution.

## 5. Static method hiding versus instance overriding

```java
class Parent {
    static void type() { System.out.println("Parent static"); }
    void run() { System.out.println("Parent instance"); }
}
class Child extends Parent {
    static void type() { System.out.println("Child static"); }
    @Override void run() { System.out.println("Child instance"); }
}
Parent value = new Child();
value.type(); // Parent static (resolved using reference type)
value.run();  // Child instance (runtime dispatch)
```

Calling a static method through an object reference is legal but discouraged; use the class name to make the compile-time binding clear.

## 6. Comparison

| Point | Overloading | Overriding |
|---|---|---|
| Parameter list | Must differ | Same signature |
| Typical location | Same class or inherited overload set | Parent and subclass |
| Selection | Compile time | Runtime for instance methods |
| Return type | Cannot distinguish overloads | Same or covariant |
| Static method | Can be overloaded | Hidden, not overridden |
| Private method | Can be overloaded | Cannot be overridden |

## 7. Interview questions

**Q1. Can main() be overloaded?**  
Yes. The JVM launch entry point is the standard `main(String[])` signature; other overloads are ordinary methods.

**Q2. Can static methods be overridden?**  
No. A static method is hidden by a same-signature static method in a subclass.

**Q3. Can an overriding method be private?**  
No, because it would reduce visibility. A private parent method is not inherited for overriding.

**Q4. Can we overload by changing only the return type?**  
No. The call would be ambiguous because the parameter list is unchanged.

**Q5. Which type decides an overloaded call?**  
The compile-time declared types of the arguments, plus applicable conversion rules.

**Q6. Which type decides an overridden call?**  
The actual runtime class of the receiver object.

## 8. Practice

1. Create overloads accepting `int`, `long`, `Integer` and `Number`; predict which overload is selected for different arguments.
2. Create a parent and child with one overridden method and one hidden static method.
3. Try reducing visibility or widening checked exceptions in an override; read the compiler error.
4. Use a parent reference pointing to a child object and distinguish overload selection from runtime dispatch.
