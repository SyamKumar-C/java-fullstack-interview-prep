# Java Pass-by-Value, final and static

## 1. Java is always pass-by-value

Java passes a copy of the argument value to a method. For an object, the copied value is the reference. The method can use that reference to mutate the same object, but reassigning the parameter does not change the caller's variable.

```java
void updateName(Employee employee) {
    employee.setName("New"); // changes the same object
}

void replace(Employee employee) {
    employee = new Employee(); // only changes local parameter
}
```

This is why Java is not pass-by-reference.

## 2. final variable

A final local variable or field can be assigned only once. For a reference, final prevents reassignment of the reference; it does not make the referenced object immutable.

```java
final List<String> names = new ArrayList<>();
names.add("Asha");       // allowed
// names = new ArrayList<>(); // not allowed
```

A final field should be initialized in a declaration, initializer or constructor.

## 3. static

A static member belongs to the class rather than to each object. Static methods do not have an instance `this` and cannot directly access instance members without an object reference.

```java
class Employee {
    static int count;
    Employee() { count++; }
    static int totalEmployees() { return count; }
}
```

Static state is shared within the class loader, not automatically shared across separate JVM processes.

## 4. static initialization

Static fields and static initializer blocks run when the class is initialized. Initialization order follows declaration order within the class, after required superclass initialization.

## 5. final, finally and finalize

- final: language modifier.
- finally: exception-handling block that normally runs after try/catch completion.
- finalize(): deprecated finalization mechanism; do not use it for resource cleanup. Prefer try-with-resources and AutoCloseable.

## 6. Practical scenarios

- Use final fields for immutable object design.
- Use static constants for shared fixed values.
- Avoid mutable global static state because it complicates testing and concurrency.
- Use try-with-resources for files, sockets and database resources.

## 7. Common mistakes

- Saying Java passes objects by reference.
- Assuming final makes an object immutable.
- Using static for data that belongs to each user/request.
- Using finalize() to close resources.
- Assuming static state is shared between application servers.

## 8. Interview questions

**Q1. Is Java pass-by-value or pass-by-reference?** Always pass-by-value; object references are copied as values.

**Q2. Can a final object be modified?** Its reference cannot be reassigned, but its mutable state can change.

**Q3. Can a static method access instance fields?** Only through an object reference.

**Q4. Is static data shared across JVMs?** No. It is associated with a class loaded in a particular JVM/class loader.

**Q5. final vs finally vs finalize?** Modifier, exception cleanup block, and deprecated finalization hook respectively.

## 9. Practice

Write a method that mutates an object's field and another that reassigns its parameter. Predict the caller's output and explain why.
