# Java Initialization Order

## 1. What happens when an object is created?

Java initializes class-level state and then instance state in a defined order. Understanding this helps explain constructor output, inheritance behavior, and null/default values.

For a class, the broad order is:
1. Class initialization: static fields and static initializer blocks, in textual order.
2. When creating an object: instance fields and instance initializer blocks, in textual order.
3. Constructor body.

For inheritance, the superclass is initialized before the subclass. During object construction, superclass instance initialization and constructor execution happen before subclass instance initialization and constructor execution.

## 2. Static initialization

```java
class Parent {
    static { System.out.println("Parent static"); }
}
class Child extends Parent {
    static { System.out.println("Child static"); }
}
```

First active use that initializes Child causes Parent class initialization before Child. Static initialization normally happens once per class loader.

## 3. Instance initialization with inheritance

```java
class Parent {
    int p = print("Parent field");
    { print("Parent block"); }
    Parent() { print("Parent constructor"); }
    int print(String s) { System.out.println(s); return 1; }
}
class Child extends Parent {
    int c = print("Child field");
    { print("Child block"); }
    Child() { print("Child constructor"); }
}
```

When `new Child()` runs, the parent portion is initialized first, then the child portion. Within each class, field initializers and instance initializer blocks run in source order before that class's constructor body.

## 4. Default values

Before explicit instance field initialization, fields receive default values:
- Numeric primitives: 0 (or 0.0).
- boolean: false.
- char: '\u0000'.
- Reference types: null.

Local variables do not receive automatic default values; they must be definitely assigned before use.

## 5. Constructor chaining

`this(...)` calls another constructor in the same class. `super(...)` calls a superclass constructor. In traditional Java syntax, one of them must be the first constructor statement. Newer Java language versions have evolving constructor-body rules, so use the syntax supported by the project's source level.

A constructor that does not explicitly call this or super gets an implicit no-argument super() call only when that superclass constructor is accessible.

## 6. Static versus instance members

Static members belong to the class; instance members belong to each object. Static initialization cannot directly use a particular object's instance state.

## 7. Common mistakes

- Assuming subclass fields initialize before superclass constructor execution.
- Using overridable methods from constructors; subclass state may not yet be initialized.
- Assuming local variables have default values.
- Forgetting that static initialization is per class loader.
- Assuming an implicit super() works when the parent has no accessible no-argument constructor.

## 8. Interview questions and answers

**Q1. What is the initialization order?**  
Class initialization runs superclass before subclass, with static fields/blocks in source order. Object construction initializes the superclass instance first, then subclass instance fields/blocks, then each constructor body.

**Q2. Do local variables get default values?**  
No. They must be definitely assigned before use.

**Q3. What is the difference between static and instance initialization blocks?**  
Static blocks run once when the class is initialized. Instance blocks run for each object creation before the constructor body.

**Q4. Why avoid calling overridable methods from constructors?**  
A subclass override can execute before subclass fields and initializers have run, observing incomplete state.

**Q5. Does a class always get an implicit no-argument constructor?**  
Only if no constructor is declared. If any constructor is declared, the compiler does not add the default no-argument constructor.

## 9. Practice

Create Parent and Child classes with static fields, static blocks, instance fields, instance blocks, and constructors. Predict the exact output before running the program.

## 10. Quick revision

Static parent -> static child during class initialization. For an object: parent fields/blocks -> parent constructor -> child fields/blocks -> child constructor. Local variables require explicit assignment.
