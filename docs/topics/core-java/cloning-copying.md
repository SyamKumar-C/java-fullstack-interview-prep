# Java Object Cloning and Copying

## 1. Copying an object

Copying means creating another object with selected values from an existing object. The important question is whether nested mutable objects are shared or copied.

## 2. Shallow copy

A shallow copy creates a new outer object but copies references to nested objects. Both objects can point to the same mutable child.

```java
class Address {
    String city;
}
class Employee implements Cloneable {
    String name;
    Address address;

    @Override
    protected Employee clone() throws CloneNotSupportedException {
        return (Employee) super.clone();
    }
}
```

After clone(), changing `copy.address.city` can also be visible through the original because the Address reference is shared.

## 3. Deep copy

A deep copy also copies the mutable nested objects that must be independent. It can be implemented with copy constructors, factory methods or carefully designed serialization.

```java
class Employee {
    String name;
    Address address;

    Employee(Employee source) {
        this.name = source.name;
        this.address = new Address(source.address);
    }
}
```

Deep copy must define which parts of the object graph are copied and which immutable/shared references can safely remain shared.

## 4. Cloneable and clone()

Cloneable is a marker interface. It does not declare clone(). Object.clone() performs a field-by-field shallow copy and is protected. The class normally exposes a suitable public/protected clone method.

The clone mechanism has awkward rules and is often avoided in modern code.

## 5. Copy constructors and factories

Copy constructors are explicit and type-safe:

```java
User(User source) {
    this.id = source.id;
    this.name = source.name;
}
```

A static factory such as `User.copyOf(source)` can make intent clearer and handle null/validation rules.

## 6. Immutable objects

If an object and all its reachable state are immutable, sharing it is safe. This often removes the need for defensive deep copies.

## 7. Common mistakes

- Calling a shallow copy a deep copy.
- Copying a collection but not its mutable elements.
- Forgetting null handling or cyclic references in manual deep-copy logic.
- Using serialization for copying without considering performance and security.
- Sharing mutable nested state between supposedly independent objects.

## 8. Interview questions

**Q1. Shallow vs deep copy?** Shallow copy duplicates the outer object but shares nested references; deep copy duplicates the required nested mutable objects too.

**Q2. What does Object.clone() do?** It creates a field-by-field shallow copy.

**Q3. Is Cloneable a method-bearing interface?** No, it is a marker interface.

**Q4. Preferred alternative to clone()?** A copy constructor or static copy factory.

## 9. Practice

Create an Order containing a mutable Address and a list of OrderItems. Implement shallow and deep copy methods, then prove which nested changes are shared.
