# Iterators, Comparable and Comparator

## Iterator
Traverses using hasNext() and next(). Its optional remove() removes the last returned element when supported. Fail-fast iterators may throw ConcurrentModificationException after external structural modification; this is best-effort detection, not thread safety.

## Comparable
Defines natural ordering using compareTo().
```java
record Employee(int id, String name) implements Comparable<Employee> {
  public int compareTo(Employee other) {
    return Integer.compare(id, other.id);
  }
}
```

## Comparator
Defines an external/alternate order.
```java
Comparator<Employee> byName = Comparator.comparing(Employee::name);
Comparator<Employee> byIdDesc =
    Comparator.comparingInt(Employee::id).reversed();
```
Compose using thenComparing. Avoid subtraction because it can overflow.

Sorted sets/maps use comparison result zero as key equivalence. Prefer consistency with equals.

**Interview:** Comparable vs Comparator? Natural order implemented by the type vs a separate ordering strategy.
