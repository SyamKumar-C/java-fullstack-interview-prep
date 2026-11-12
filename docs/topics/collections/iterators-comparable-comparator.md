# Java Iterators, Comparable and Comparator

## 1. Iterator

Iterator traverses a collection without exposing its internal representation.

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    String name = it.next();
    if (name.isBlank()) {
        it.remove(); // removes the last element returned by next()
    }
}
```

- `hasNext()`: whether another element exists.
- `next()`: returns the next element.
- `remove()`: optional operation; removes the last element returned by next.

Calling next when exhausted throws NoSuchElementException. Calling remove before next, or twice for the same element, throws IllegalStateException. Some iterators do not support remove and throw UnsupportedOperationException.

## 2. ListIterator

ListIterator is specific to List and supports:
- Forward and backward traversal.
- nextIndex and previousIndex.
- add and set.
- remove.

It is useful when a list must be edited while traversing in a controlled way.

## 3. Fail-fast versus weakly consistent versus snapshot iterators

| Iterator style | Behavior | Example |
|---|---|---|
| Fail-fast | May throw ConcurrentModificationException after unexpected structural modification | ArrayList iterator |
| Weakly consistent | Can reflect some concurrent changes; does not fail fast | ConcurrentHashMap iterator |
| Snapshot | Iterates over a stable captured array/version | CopyOnWriteArrayList iterator |

Fail-fast is best-effort bug detection, not a thread-safety guarantee. It does not detect every race and must never be used for coordination.

## 4. Comparable: natural ordering

Comparable defines natural ordering inside the class through `compareTo`.

```java
class Employee implements Comparable<Employee> {
    private final int id;

    Employee(int id) { this.id = id; }

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id);
    }
}
```

A negative value means this object is less than the other; zero means equal in ordering; positive means greater. It does not need to return exactly -1, 0 or 1.

Avoid subtraction such as `a.id - b.id`; integer overflow can produce an incorrect result.

Natural ordering should be consistent with equals where practical. TreeSet and TreeMap use comparison to determine uniqueness/key equivalence.

## 5. Comparator: external ordering

Comparator defines an ordering outside the class. It is useful when one type needs multiple sort rules.

```java
Comparator<Employee> byId =
    Comparator.comparingInt(Employee::getId);

Comparator<Employee> bySalaryDesc =
    Comparator.comparingInt(Employee::getSalary).reversed();

Comparator<Employee> byDepartmentThenName =
    Comparator.comparing(Employee::getDepartment)
              .thenComparing(Employee::getName);
```

Use:
- `thenComparing` for tie-breakers.
- `nullsFirst` / `nullsLast` when null values are valid.
- `comparingLong` / `comparingDouble` for corresponding primitive keys.
- `reversed()` to reverse order.

## 6. Where should a Comparator be written?

- Inline at sort call for a one-time simple rule.
- Static constant in a model/utility class for reusable ordering.
- Dedicated class for complex reusable comparison logic.

```java
employees.sort(Comparator.comparingInt(Employee::getId));
```

Avoid subtracting numeric fields and avoid comparators that are inconsistent or non-transitive.

## 7. Comparable versus Comparator

| Comparable | Comparator |
|---|---|
| Implemented by the class | Separate object/lambda |
| Defines natural order | Defines alternative/custom order |
| compareTo(T) | compare(T, T) |
| Usually one primary order | Multiple orders can coexist |

## 8. Interview questions and answers

**Q1. Iterator versus ListIterator?**  
Iterator supports forward traversal and optional removal. ListIterator adds backward traversal, indexes, add and set for lists.

**Q2. What is fail-fast?**  
An iterator may detect structural modification outside itself and throw ConcurrentModificationException. This is best-effort detection, not synchronization.

**Q3. Comparable versus Comparator?**  
Comparable defines natural order in the type; Comparator defines external/custom ordering.

**Q4. What does compare return?**  
Negative, zero or positive to indicate ordering. Exact values -1 and 1 are not required.

**Q5. Why use Integer.compare rather than subtraction?**  
Subtraction can overflow and return the wrong ordering.

**Q6. Why can TreeSet reject unequal objects?**  
If the comparator returns zero, TreeSet treats them as the same ordering key.

**Q7. What is a snapshot iterator?**  
An iterator over a stable captured view; later collection changes are not reflected in that iterator.

## 9. Practice

1. Sort employees by salary descending, then name ascending.
2. Use ListIterator to replace and insert elements.
3. Compare ArrayList fail-fast iteration with CopyOnWriteArrayList snapshot iteration.
4. Build a TreeSet comparator that returns zero for distinct employees and explain the result.
5. Fix a comparator that uses subtraction and may overflow.
