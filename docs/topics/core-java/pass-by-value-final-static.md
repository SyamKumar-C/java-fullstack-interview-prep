# Pass-by-Value, final and static

## Java is always pass-by-value
Java passes a copy of a value. For an object, the copied value is its reference. A method can mutate the referenced object, but reassigning its local parameter does not reassign the caller's variable.

```java
static void changeName(Employee e) { e.name = "Mira"; }
static void replace(Employee e) { e = new Employee("New"); }
// changeName mutates the shared object; replace changes only its local copy.
```

For primitives, the copied value is independent of the caller's variable.

## final
- final local variable: assigned once.
- final field: initialized once, at declaration/initializer/constructor.
- final method: cannot be overridden.
- final class: cannot be subclassed.
- final reference: cannot be reassigned, but its object may still be mutable.

```java
final List<String> names = new ArrayList<>();
names.add("A"); // allowed
// names = new ArrayList<>(); // compile error
```

## static
Static fields and methods belong to the class. Static methods have no `this` and cannot directly access instance members. Static initialization occurs during class initialization.

## Interview checks
**Does Java pass objects by reference?** No. It passes the reference value by value.
**Does final make an object immutable?** No. It prevents reassignment, not mutation.
