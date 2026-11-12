# Java Classes, Objects and Constructors

## 1. Class, object and reference

A **class** defines fields, constructors and methods. An **object** is a runtime instance. A **reference variable** stores a reference value used to access an object; it is not the object itself.

```java
class Employee {
    private long id;
    private String name;

    Employee(long id, String name) {
        this.id = id;
        this.name = name;
    }

    void work() {
        System.out.println(name + " is working");
    }
}

Employee employee = new Employee(101, "Asha");
employee.work();
```

Each instance has its own instance fields. Static fields belong to the class (more precisely, the class as loaded by a class loader).

## 2. Object creation and initialization

For a normal `new SomeClass(...)` expression, the important conceptual steps are:

1. Ensure the class has been initialized if it has not already been initialized.
2. Allocate and default-initialize instance fields (zero, false or null).
3. Execute the constructor chain: superclass initialization and constructor first, then subclass initialization and constructor.
4. Return the reference to the caller.

The JVM may optimize allocation, so source-level `new` does not guarantee a simple physical heap allocation in every optimized execution.

## 3. Constructors

A constructor has the class name and no return type. It establishes a valid initial state.

- If no constructor is declared, the compiler provides a default no-argument constructor.
- If any constructor is declared, the compiler does not add that default constructor.
- Constructors can be overloaded.
- Constructors are not inherited and cannot be overridden.
- A constructor may be private to control object creation (factory or singleton-style designs).
- Constructors cannot be static, final or abstract.

```java
class Employee {
    private final long id;
    private final String name;

    Employee(long id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

## 4. Constructor chaining: this() and super()

`this(...)` invokes another constructor in the same class. `super(...)` invokes a superclass constructor. Under Java 21 rules, an explicit `this(...)` or `super(...)` constructor invocation must be the first statement.

```java
class Person {
    private final String name;
    Person(String name) { this.name = name; }
}

class Developer extends Person {
    Developer() {
        this("Unknown");
    }

    Developer(String name) {
        super(name);
    }
}
```

If neither is written, the compiler inserts `super()`. Compilation fails if the parent has no accessible no-argument constructor.

A constructor cannot invoke both `this(...)` and `super(...)` directly. The chain eventually reaches a superclass constructor.

## 5. Field initialization and initializer blocks

Within each class, instance field initializers and instance initializer blocks run in textual order before that class's constructor body. The superclass portion is initialized before the subclass portion.

Static field initializers and static blocks run during class initialization, in textual order, after required superclass initialization.

Do not call overridable methods from constructors: a child override may run before the child's fields have been initialized.

## 6. Instance versus static members

| Instance member | Static member |
|---|---|
| Belongs to each object | Belongs to class initialization context |
| Accessed through an object | Best accessed through class name |
| Instance method has `this` | Static method has no `this` |
| Can directly access instance and static members | Cannot directly access instance members |

Static state is shared by instances of that class in the same class loader. It is not automatically shared across JVM processes or separate application instances.

## 7. this, super and constructor use cases

- `this.field`: distinguish an instance field from a parameter with the same name.
- `this(...)`: reuse constructor initialization logic.
- `super.method()`: call the parent implementation.
- `super(...)`: choose the parent constructor.
- Private constructor: restrict direct construction.
- Static factory: provide a named creation method, validation or cached instances.

## 8. Common mistakes

- Declaring a constructor with a return type; that is a method.
- Expecting a default constructor after declaring a parameterized constructor.
- Forgetting the implicit `super()` call.
- Calling overridable methods during construction.
- Treating a reference variable as the object itself.
- Using mutable static fields for request-specific data.

## 9. Interview questions and answers

**Q1. What is the difference between a class and an object?**  
A class defines a type; an object is an instance of that type at runtime.

**Q2. Are constructors inherited or overridden?**  
No. A child constructor explicitly or implicitly invokes a parent constructor.

**Q3. When does the compiler generate a default constructor?**  
Only when the class declares no constructor at all.

**Q4. Can a constructor be private?**  
Yes. It is used to control construction, often with static factory methods.

**Q5. What happens if the parent has only a parameterized constructor?**  
The child must explicitly call an accessible parent constructor with matching arguments; implicit `super()` will fail.

**Q6. Can a static method access an instance field?**  
Only through an object reference. It has no `this`.

**Q7. Why should constructors avoid calling overridable methods?**  
The child override can run before child initialization is complete and observe default or incomplete values.

## 10. Practice

1. Create a class with no constructor and inspect the generated no-argument behavior.
2. Add a parameterized constructor and verify that `new Employee()` no longer compiles.
3. Create a parent with only a parameterized constructor and chain child constructors correctly.
4. Add static and instance initializer blocks to parent and child classes; predict the exact output.
5. Implement a private constructor and a validated static factory method.
