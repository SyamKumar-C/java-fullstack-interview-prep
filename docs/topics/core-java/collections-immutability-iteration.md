# Immutable Collections, Iterators and Fail-Fast Behavior

> Priority: P1 | Level: Intermediate | Study time: 35 minutes

## 1. Immutable vs unmodifiable
An unmodifiable collection rejects changes through that reference. It may still reflect changes made through another reference to its backing collection. List.copyOf creates an unmodifiable snapshot of the elements, but it is shallow: mutable elements can still change.

```java
List<String> source = new ArrayList<>(List.of("A", "B"));
List<String> view = Collections.unmodifiableList(source);
source.add("C");
System.out.println(view); // [A, B, C]

List<String> copy = List.copyOf(source);
source.add("D");
System.out.println(copy); // remains [A, B, C]
```

List.of and List.copyOf return unmodifiable lists and reject null elements.

## 2. Iterator and ListIterator
Iterator supports forward traversal and optional removal through remove(). ListIterator is for List and supports forward/backward traversal, index information, set, and add.

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    if (it.next().isBlank()) {
        it.remove();
    }
}
```

Use iterator.remove when removing during iteration. Do not structurally modify an ArrayList through the list itself while its ordinary iterator is active.

## 3. Fail-fast
Many general-purpose iterators detect structural modification outside the iterator and may throw ConcurrentModificationException. This is a best-effort bug detector, not a thread-safety guarantee. It may not detect every race, so never use it for thread coordination.

## 4. Safe alternatives
- Iterator.remove for supported removal during iteration.
- removeIf for predicate-based removal.
- Concurrent collections for concurrent access patterns.
- Defensive copy when a stable snapshot is needed.

```java
names.removeIf(String::isBlank);
```

## 5. Common mistakes
- Calling an unmodifiable view deeply immutable.
- Assuming List.copyOf deep-copies elements.
- Modifying ArrayList inside enhanced for.
- Treating ConcurrentModificationException as synchronization.
- Assuming fail-fast is guaranteed in every race.

## 6. Interview questions
**Q1. unmodifiableList vs List.copyOf?** The former is a view of the supplied list; the latter is an unmodifiable snapshot of its elements.

**Q2. Is List.copyOf deeply immutable?** No. Mutable element objects can still change.

**Q3. What is fail-fast?** An iterator may detect unexpected structural modification and throw ConcurrentModificationException.

**Q4. Is fail-fast thread safety?** No. It is only best-effort detection.

## 7. Practice
1. Show how an unmodifiable view reflects backing-list changes.
2. Compare it with List.copyOf.
3. Remove blank strings using iterator.remove and removeIf.
