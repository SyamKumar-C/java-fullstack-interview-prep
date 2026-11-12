# Immutable Collections, Iterators and Fail-Fast Behavior

## 1. Unmodifiable view versus immutable snapshot

An unmodifiable view rejects writes through that view, but it can reflect changes made through another reference to the backing collection. `List.copyOf` returns an unmodifiable snapshot of the element references.

```java
List<String> source = new ArrayList<>(List.of("A", "B"));
List<String> view = Collections.unmodifiableList(source);
source.add("C");
System.out.println(view); // [A, B, C]

List<String> snapshot = List.copyOf(source);
source.add("D");
System.out.println(snapshot); // [A, B, C]
```

`List.of` and `List.copyOf` reject null elements. Both produce unmodifiable lists.

## 2. Shallow immutability

An unmodifiable collection does not make its elements immutable. If a list contains mutable Employee objects, callers may still change an Employee's fields unless those objects are themselves immutable or defensively copied.

For a defensive snapshot of mutable elements, copy the elements too (or use immutable value types). A shallow copy only protects the container structure.

## 3. Iterator and ListIterator

Iterator supports forward traversal and optional removal through `remove()`. ListIterator is List-specific and supports forward/backward traversal, indexes, set and add.

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    if (it.next().isBlank()) {
        it.remove();
    }
}
```

## 4. Fail-fast, weakly consistent and snapshot behavior

- Fail-fast iterators (for example ArrayList) may throw ConcurrentModificationException after unexpected structural changes.
- Weakly consistent iterators (for example ConcurrentHashMap) can reflect some concurrent updates and do not fail fast.
- Snapshot iterators (for example CopyOnWriteArrayList) traverse a stable captured array.

Fail-fast is best-effort bug detection, not a concurrency control mechanism. It is not guaranteed to detect every race.

## 5. Safe ways to modify during traversal

- Use Iterator.remove when supported.
- Use ListIterator.add/set for list edits.
- Use `removeIf` for predicate-based removal.
- Use a defensive copy when a stable snapshot is required.
- Use a concurrent collection when concurrent updates are part of the design.

```java
names.removeIf(String::isBlank);
```

## 6. Common mistakes

- Calling an unmodifiable view deeply immutable.
- Assuming List.copyOf deep-copies its elements.
- Structurally modifying an ArrayList inside an enhanced for loop.
- Treating ConcurrentModificationException as synchronization.
- Assuming fail-fast always detects concurrent races.
- Confusing an unmodifiable API response with a thread-safe shared object.

## 7. Interview questions

**Q1. unmodifiableList vs List.copyOf?**  
The former is an unmodifiable view of the supplied list; the latter returns an unmodifiable snapshot of its element references.

**Q2. Is List.copyOf deeply immutable?**  
No. Mutable objects inside the list can still change.

**Q3. What is fail-fast?**  
An iterator may detect unexpected structural modification and throw ConcurrentModificationException.

**Q4. Is fail-fast thread safety?**  
No. It is best-effort error detection.

**Q5. Snapshot vs weakly consistent iterator?**  
A snapshot iterator sees a captured version; a weakly consistent iterator may reflect some concurrent updates while traversing.

## 8. Practice

1. Show how an unmodifiable view reflects backing-list changes.
2. Compare it with List.copyOf.
3. Put a mutable object in a copied list and show why the copy is shallow.
4. Remove blank strings using iterator.remove and removeIf.
5. Compare fail-fast, weakly consistent and snapshot iteration.
