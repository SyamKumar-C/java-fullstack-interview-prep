# Java Classes, Objects and Constructors

## 1. Class vs object

A class defines fields and methods. An object is a runtime instance with its own instance state.

```java
class Employee {
    int id;
    String name;
    void work() { System.out.println(name + " is working"); }
}
Employee employee = new Employee();
employee.id = 101;
employee.name = "Asha";
employee.work();
```

## 2. What happens during object creation?

For a normal `new Employee()` expression, Java initializes the object fields to default values, runs the constructor chain (including the superclass constructor), and returns a reference. A local variable holds the reference, not the object itself. JVM optimizations mean source-level `new` does not always require a physical heap allocation.

## 3. Constructors

A constructor has the class name and no return type. It initializes a new object.

```java
class Employee {
    private final long id;
    private String name;

    Employee(long id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

- If you declare no constructor, the compiler provides a no-argument default constructor.
- If you declare any constructor, no default no-argument constructor is added.
- Constructors can be overloaded, but are not inherited or overridden.
- Constructors can be private to control object creation.

## 4. Constructor chaining

`this(...)` calls another constructor in the same class. `super(...)` calls a parent constructor. The explicit constructor invocation must appear first (under the language rules for the Java version being used).

```java
class Person {
    Person(String name) { System.out.println(name); }
}
class Developer extends Person {
    Developer() { this("Unknown"); }
    Developer(String name) {
        super(name);
        System.out.println("Developer");
    }
}
```

If no constructor invocation is written, Java inserts `super()`. Compilation fails if the parent has no accessible no-argument constructor.

## 5. Instance vs static members

Instance fields belong to each object. Static fields belong to the class and are shared by instances in that class loader. Static methods have no `this` and cannot directly access instance members.

Avoid mutable static state for request-specific data because it is shared by threads.

## 6. Interview questions

**Can constructors be inherited?** No. A child constructor must call an accessible parent constructor.

**Can a constructor be static, final or abstract?** No.

**What is the difference between a reference and an object?** The object contains state; the reference is a value used to access it.

**Why use a private constructor?** To control instantiation, such as in utility/factory designs.

## 7. Practice

Create a class with overloaded constructors, chain them using this(), call a parent constructor using super(), and maintain a static count of created instances.
