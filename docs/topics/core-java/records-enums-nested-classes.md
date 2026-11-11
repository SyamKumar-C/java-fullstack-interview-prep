# Records, Enums and Nested Classes

## 1. Records

A record is a concise data carrier. The compiler generates a canonical constructor, accessors, equals, hashCode and toString.

```java
public record EmployeeDto(long id, String name) {}
EmployeeDto dto = new EmployeeDto(10, "Ravi");
System.out.println(dto.name());
```

Record components are final, so records are shallowly immutable. A mutable object referenced by a component can still be changed unless defensively copied. Records can implement interfaces but cannot extend another class.

Use records for DTOs/value-like data, not automatically for every JPA entity.

## 2. Enums

Enums define a fixed set of constants and can contain fields, constructors and methods.

```java
enum Priority {
    LOW(1), HIGH(2);
    private final int level;
    Priority(int level) { this.level = level; }
    int level() { return level; }
}
```

Enum constructors cannot be public or protected. Compare enum constants with ==. Avoid persisting ordinal values if enum order may change; stable names/codes are safer.

## 3. Nested classes

| Type | Meaning |
|---|---|
| Static nested | No enclosing object required |
| Inner member | Associated with an enclosing instance |
| Local | Declared inside a method/block |
| Anonymous | One-off class expression |

```java
class Outer {
    private int value = 10;
    static class Nested { }
    class Inner { void show() { System.out.println(value); } }
}
```

Create an inner class using `outer.new Inner()`. A static nested class has no implicit outer instance.

## 4. Interview questions

**Are records fully immutable?** No, only component references are final; referenced mutable values may change.

**Can enum have a constructor?** Yes, private/package-private only.

**Can enum extend another class?** No, it already extends java.lang.Enum, but it can implement interfaces.

**Static nested vs inner?** Inner is tied to an outer object; static nested is not.

## 5. Practice

Create an OrderStatus enum with a display label, an OrderResponse record and a static nested helper. Explain why the record is suitable for a response DTO.
