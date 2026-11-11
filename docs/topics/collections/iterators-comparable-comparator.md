# Java Iterators, Comparable and Comparator

## 1. Iterator

An Iterator traverses a collection without exposing its internal structure.

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    String name = it.next();
    if (name.isBlank()) {
        it.remove(); // supported removal through this iterator
    }
}
```

- hasNext checks whether another element exists.
- next returns the next element.
- remove removes the last element returned by next, when supported.

Calling next without an available element throws NoSuchElementException. Calling remove before next or more than once for the same element can throw IllegalStateException.

## 2. Fail-fast behavior

Many standard collection iterators are fail-fast: if the collection is structurally modified outside the iterator during iteration, they may throw ConcurrentModificationException.

Fail-fast is best-effort bug detection, not a thread-safety guarantee. It does not mean every concurrent modification is always detected.

Use iterator.remove for supported removal during iteration, or use concurrent collections when concurrent modification is part of the design.

## 3. ListIterator

ListIterator is available for Lists and supports:
- Forward and backward traversal.
- nextIndex and previousIndex.
- add and set during iteration.
- remove.

It is useful when a list must be traversed and modified in a controlled way.

## 4. Comparable

Comparable defines a class's natural ordering using `compareTo`.

```java
class Employee implements Comparable<Employee> {
    private final int id;
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id);
    }
}
```

A negative result means this object is less than the other; zero means equal in ordering; positive means greater. Avoid subtraction such as `a.id - b.id` because integer overflow can produce incorrect ordering.

Natural ordering should be consistent with equals where practical, especially when using sorted sets/maps, because those collections treat compareTo returning zero as the same ordering key.

## 5. Comparator

Comparator defines an external ordering. It is useful when one class needs multiple sort orders.

```java
Comparator<Employee> byName =
    Comparator.comparing(Employee::getName);

Comparator<Employee> bySalaryDesc =
    Comparator.comparingInt(Employee::getSalary).reversed();

Comparator<Employee> byDepartmentThenName =
    Comparator.comparing(Employee::getDepartment)
              .thenComparing(Employee::getName);
```

Use `thenComparing` to add tie-breakers. Use nullsFirst/nullsLast when null values are valid.

## 6. Comparable versus Comparator

| Comparable | Comparator |
|---|---|
| Implemented by the class | Separate object/lambda |
| Defines natural ordering | Defines alternative orderings |
| Method: compareTo | Method: compare |
| One primary natural order | Many custom orderings |

## 7. Where are comparators written?

A Comparator can be declared:
- As a static constant in the model or utility class.
- Inline at the sort call for a one-time rule.
- In a dedicated comparator class for reusable complex logic.

```java
employees.sort(Comparator.comparingInt(Employee::getId));
```

## 8. Interview questions and answers

**Q1. Iterator versus ListIterator?**  
Iterator supports forward traversal and optional removal. ListIterator is List-specific and supports backward traversal, insertion, replacement, and index information.

**Q2. What is fail-fast?**  
An iterator may detect structural modification outside itself and throw ConcurrentModificationException. It is best-effort detection, not synchronization.

**Q3. Comparable versus Comparator?**  
Comparable defines natural order inside the class; Comparator defines an external/custom order.

**Q4. What does compare return?**  
A negative, zero, or positive integer indicating ordering. It need not return exactly -1, 0, or 1.

**Q5. Why use Integer.compare instead of subtraction?**  
Subtraction can overflow and reverse the intended ordering.

**Q6. Why does TreeSet sometimes remove two unequal objects?**  
If its comparator returns zero for them, TreeSet treats them as the same ordering key. Ensure comparator semantics match the uniqueness requirement.

## 9. Practice

Sort employees by salary descending, then name ascending. Create a TreeSet and demonstrate what happens when the comparator returns zero for two distinct employees.

## 10. Quick revision

Iterator traverses; ListIterator adds backward/list-specific operations. Comparable = natural order. Comparator = custom order. Use compare methods, not subtraction.
