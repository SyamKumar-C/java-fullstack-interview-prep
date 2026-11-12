# Records, Enums and Nested Classes (Java 21)

## 1. Records

A record is a concise class form for data carriers. The compiler provides a canonical constructor, component accessors, `equals()`, `hashCode()` and `toString()`.

```java
public record EmployeeDto(long id, String name) {}
EmployeeDto dto = new EmployeeDto(10, "Ravi");
System.out.println(dto.name()); // record accessor, not getName()
```

Important rules:

- Record components become private final fields.
- Records are implicitly final and cannot extend another class.
- A record can implement interfaces.
- Records cannot declare additional instance fields; static fields are allowed.
- You can add methods, validation and constructors.
- Records are shallowly immutable: a referenced mutable object may still change.

### Compact constructor and validation

```java
public record Range(int start, int end) {
    public Range {
        if (start > end) {
            throw new IllegalArgumentException("start must not exceed end");
        }
    }
}
```

A compact constructor validates or normalizes components without repeating the parameter list and assignments.

Use records for DTOs and value-like data when component-based equality is correct. Do not assume a record is automatically a good JPA entity; JPA entities usually need identity and lifecycle behavior that records do not provide.

## 2. Enums

An enum represents a fixed set of instances. Enums can have fields, constructors, methods and implement interfaces.

```java
enum Priority {
    LOW(1), MEDIUM(2), HIGH(3);

    private final int level;
    Priority(int level) { this.level = level; }
    int level() { return level; }
}
```

- Enum constructors cannot be public or protected.
- Enum constants are singleton instances per enum constant/class loader.
- Enums can implement interfaces but cannot extend another class because they already extend `java.lang.Enum`.
- Compare enum constants using `==`.
- Avoid using `ordinal()` as a persistent database/API value; reordering constants changes ordinals. Persist a stable name or explicit code instead.
- `values()` returns constants; `valueOf(String)` looks up by exact constant name and throws IllegalArgumentException if not found.

## 3. Nested classes

| Type | Description | Outer instance required? |
|---|---|---|
| Static nested | Member class declared static | No |
| Inner member | Non-static member class | Yes |
| Local | Declared inside a method/block | Enclosing context |
| Anonymous | One-off class expression | Depends on context |

```java
class Outer {
    private int value = 10;

    static class Nested {
        void show() { System.out.println("No Outer instance needed"); }
    }

    class Inner {
        void show() { System.out.println(value); }
    }
}
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
Outer.Nested nested = new Outer.Nested();
```

An inner class has an implicit association with an enclosing Outer instance and can access its private members. A static nested class has no implicit outer instance.

Local classes and lambdas can capture local variables only when they are final or effectively final. An anonymous class is useful for a one-off implementation, though lambdas are usually clearer for functional interfaces.

## 4. Common interview traps

- Record accessors are `name()`, not JavaBean `getName()` by default.
- A record containing a List is not deeply immutable just because the component reference is final.
- Enum ordinal is not a stable business identifier.
- Inner and static nested classes are not the same.
- A record cannot extend a user-defined base class.
- Enum constructors are not public.

## 5. Interview questions and answers

**Q1. What methods does a record generate?**  
Component accessors, canonical constructor, equals, hashCode and toString (unless explicitly customized).

**Q2. Is a record deeply immutable?**  
No. Its component fields are final, but referenced mutable objects can still be mutated. Use defensive copies when needed.

**Q3. Can a record implement an interface?**  
Yes. It cannot extend another class because records already extend java.lang.Record.

**Q4. Can an enum have a constructor and fields?**  
Yes. The constructor is private or package-private, and each constant can supply constructor arguments.

**Q5. Why avoid ordinal for persistence?**  
Reordering or inserting enum constants changes ordinal values and can corrupt stored meaning.

**Q6. Static nested class versus inner class?**  
A static nested class does not require an instance of the outer class. An inner member class is associated with one outer instance.

## 6. Practice

1. Create an EmployeeResponse record with validation and a List component; make the list defensively immutable.
2. Create an enum with a stable code and a method to find a constant by code.
3. Write a static nested builder and a non-static inner class; compare how each is instantiated.
