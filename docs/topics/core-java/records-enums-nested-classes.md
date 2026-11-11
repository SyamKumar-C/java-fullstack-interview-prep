# Records, Enums and Nested Classes

## 1. Records (Java 16+)

A record is a concise way to model data-oriented classes. The compiler provides a canonical constructor, accessors, `equals()`, `hashCode()` and `toString()`.

```java
public record EmployeeDto(long id, String name) { }

EmployeeDto dto = new EmployeeDto(10, "Ravi");
System.out.println(dto.name()); // Ravi
```

Record components are final, so a record is shallowly immutable. If a component refers to a mutable list, that list can still be changed unless you defensively copy it.

Records cannot extend another class, but they can implement interfaces. They are useful for DTOs and value-like data, not as a replacement for every entity.

## 2. Enums

An enum defines a fixed set of constants.

```java
enum OrderStatus {
    NEW, PAID, SHIPPED, CANCELLED
}
```

Enums can have fields, constructors and methods. Their constructors are not public and enum constants are singleton instances.

```java
enum Priority {
    LOW(1), HIGH(2);
    private final int level;
    Priority(int level) { this.level = level; }
    int level() { return level; }
}
```

Use `==` to compare enum constants. For persistence, be careful with ordinal values because reordering enum constants can change stored meanings; storing stable names or explicit codes is often safer.

## 3. Nested class types

| Type | Key point |
|---|---|
| Static nested class | Does not need an enclosing object |
| Inner member class | Each instance is associated with an enclosing object |
| Local class | Declared inside a method/block |
| Anonymous class | One-off class expression, often for an interface or abstract class |

```java
class Outer {
    private int value = 10;

    static class Nested {
        void show() { System.out.println("No Outer instance required"); }
    }

    class Inner {
        void show() { System.out.println(value); }
    }
}
```

Create an inner class using an outer instance: `Outer.Inner inner = outer.new Inner();`.

## 4. Static nested vs inner class

A static nested class cannot directly access instance members of the outer class because it has no enclosing instance. An inner class can access them through its enclosing object.

Use a static nested class when the helper is closely related to the outer type but does not need an outer object.

## 5. Interview questions

**Are records fully immutable?** Their component references are final, but referenced mutable objects are not automatically made immutable.

**Can an enum have a constructor?** Yes, but it cannot be public or protected; constants invoke it.

**Can an enum extend a class?** No, enums already extend java.lang.Enum, but can implement interfaces.

**What is the difference between static nested and inner class?** The inner class is tied to an enclosing instance; the static nested class is not.

## 6. Practice

Create an OrderStatus enum with a display label, an OrderResponse record, and a static nested builder/helper class. Explain why a record is or is not suitable for a JPA entity.
