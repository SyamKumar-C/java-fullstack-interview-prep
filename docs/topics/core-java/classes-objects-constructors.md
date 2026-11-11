# Classes, Objects and Constructors

## 1. Class vs object

A **class** is a blueprint. An **object** is a runtime instance created from that blueprint.

```java
class Employee {
    int id;
    String name;

    void work() {
        System.out.println(name + " is working");
    }
}

Employee e = new Employee();
e.id = 101;
e.name = "Asha";
e.work(); // Asha is working
```

The class declares fields and behavior. The object carries its own instance state. Multiple objects share the class definition but normally have separate instance fields.

## 2. What happens when an object is created?

For `new Employee()`, Java:
1. Allocates memory for the object on the heap.
2. Gives fields their default values (`0`, `false`, or `null`).
3. Runs the constructor chain, starting with the superclass constructor.
4. Returns a reference to the new object.

A local variable such as `e` holds a reference; it is not the object itself. The JVM may optimize allocation, so avoid claiming every source-level `new` must result in a physical heap allocation.

## 3. Constructors

A constructor initializes a new object. Its name matches the class and it has no return type.

```java
class Employee {
    final int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

- A no-argument constructor is supplied by the compiler only when you declare no constructor.
- If you declare any constructor, Java does not add the default no-argument constructor.
- Constructors can be overloaded.
- Constructors are not inherited and cannot be overridden.
- A constructor can be private, for example to control object creation in a utility or singleton design.

## 4. Constructor chaining: this() and super()

`this(...)` calls another constructor in the same class. `super(...)` calls a superclass constructor. A constructor invocation must be the first statement in a constructor (Java versions before 25); do not put both in the same constructor.

```java
class Person {
    Person(String name) {
        System.out.println("Person: " + name);
    }
}
class Developer extends Person {
    Developer() {
        this("Unknown");
    }
    Developer(String name) {
        super(name);
        System.out.println("Developer created");
    }
}
```

Output:
```text
Person: Unknown
Developer created
```

If you omit an explicit constructor invocation, Java inserts `super()`. This causes a compile error if the parent has no accessible no-argument constructor.

## 5. Instance vs static members

- Instance fields belong to each object.
- Static fields belong to the class and are shared.
- Instance methods can access both instance and static members.
- Static methods have no `this` reference and cannot directly access instance members.

Use static state carefully: mutable static fields are shared across requests and threads.

## 6. Interview checks

**Can a constructor be inherited?** No. A subclass must call an accessible parent constructor.

**Can a constructor be final, static or abstract?** No. These modifiers do not make sense for constructors.

**What is the difference between a reference and an object?** The object contains state and behavior; a reference is a value used to reach that object.

**What if a constructor throws an exception?** The object is not successfully created and the caller receives the exception.

## 7. Practice

1. Create a class with two overloaded constructors.
2. Demonstrate constructor chaining using `this()` and `super()`.
3. Add one static counter and increment it whenever an object is created.
4. Explain why a private constructor is useful.
