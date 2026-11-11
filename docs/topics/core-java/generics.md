# Java Generics

## 1. Why generics?

Generics let classes, interfaces, and methods work with types while keeping compile-time type safety. Without generics, a collection may hold unrelated objects and require unsafe casts.

```java
List<String> names = new ArrayList<>();
names.add("Asha");
// names.add(10); // compile-time error
String first = names.get(0);
```

Generics reduce ClassCastException risk and make APIs reusable.

## 2. Generic class and method

```java
class Box<T> {
    private T value;
    void set(T value) { this.value = value; }
    T get() { return value; }
}

static <T> T first(List<T> values) {
    return values.get(0);
}
```

A type parameter such as T is a placeholder. Common conventions include T (type), E (element), K (key), V (value), and R (result).

## 3. Bounded type parameters

A bound restricts which types can be supplied.

```java
static <T extends Number> double sum(T a, T b) {
    return a.doubleValue() + b.doubleValue();
}
```

`extends` is used for both class and interface bounds. Multiple bounds are possible, with a class (if any) first: `<T extends Number & Comparable<T>>`.

## 4. Wildcards

- `?`: unknown type.
- `? extends T`: some unknown subtype of T; useful when reading values as T.
- `? super T`: some unknown supertype of T; useful when adding T values.

PECS: **Producer Extends, Consumer Super**.

```java
static double total(List<? extends Number> values) {
    return values.stream().mapToDouble(Number::doubleValue).sum();
}

static void addDefaults(List<? super Integer> values) {
    values.add(10);
    values.add(20);
}
```

For `List<? extends Number>`, you can safely read a Number, but cannot add an arbitrary Number because the actual list might be List<Integer>. For `List<? super Integer>`, you can add Integer values, but reads are only safely typed as Object.

## 5. Invariance

Even though Integer extends Number, `List<Integer>` is not a subtype of `List<Number>`. If it were, code could add a Double to a List<Integer> through a List<Number> reference.

Use wildcards to express safe variance.

## 6. Type erasure

Java implements generics primarily through type erasure. Generic type information is mostly removed during compilation, with compiler-generated casts and bridge methods where needed.

Consequences:
- Cannot normally write `new T()`.
- Cannot create `new List<String>[10]` directly.
- Cannot use primitive type arguments such as `List<int>`; use wrappers.
- Runtime checks cannot generally distinguish `List<String>` from `List<Integer>`.

## 7. Raw types

A raw type such as `List list` skips generic type checking and can cause runtime ClassCastException. Prefer parameterized types. Raw types remain for backward compatibility.

## 8. Common mistakes

- Confusing `List<Integer>` with `List<Number>`.
- Using extends when the method needs to add values.
- Using super when the method needs to read a specific subtype.
- Ignoring unchecked warnings.
- Expecting generic type parameters to be fully available at runtime.

## 9. Interview questions and answers

**Q1. Why use generics?**  
They provide compile-time type safety, reduce casts, and allow reusable typed APIs.

**Q2. Is List<Integer> a subtype of List<Number>?**  
No. Generic types are invariant.

**Q3. What is PECS?**  
Producer Extends, Consumer Super. Use extends when a structure produces values for you to read; use super when you put values into it.

**Q4. What is type erasure?**  
The compiler implements generic type constraints largely at compile time and removes most type parameter information from runtime representation.

**Q5. Why can't we create new T()?**  
The runtime generally does not know the concrete T due to erasure, and it cannot choose a constructor safely.

**Q6. Difference between extends and super wildcard?**  
extends lets you read values as the upper bound; super lets you add values of the lower-bound type.

## 10. Practice

Write a generic Pair<K,V>, a method that finds the maximum Comparable element, and a method that copies from List<? extends T> to List<? super T>.

## 11. Quick revision

Generics = compile-time type safety. Invariant types. PECS: extends for producers, super for consumers. Type erasure limits runtime generic type information.
