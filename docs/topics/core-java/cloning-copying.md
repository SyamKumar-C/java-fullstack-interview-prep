# Object Cloning and Copying

## Reference copy
`Employee b = a;` copies the reference. Both variables point to the same object.

## Shallow vs deep copy
A shallow copy duplicates the outer object but shares referenced objects. A deep copy also copies mutable nested objects to the required depth. Define behavior for cycles, shared references and immutable values.

## Cloneable
`Object.clone()` performs a field-by-field shallow copy and is protected. `Cloneable` is a marker interface that permits the native clone operation; it does not define a public cloning API or deep-copy behavior.

```java
class Address { String city; }
class Employee implements Cloneable {
  Address address;
  @Override public Employee clone() throws CloneNotSupportedException {
    Employee copy = (Employee) super.clone();
    copy.address = new Address();
    copy.address.city = address.city;
    return copy;
  }
}
```

## Better choices
Prefer copy constructors, static factories or explicit mapping code. They make copy depth and invariants clear.

**Interview:** Does clone() create a deep copy? No, its default behavior is shallow.
