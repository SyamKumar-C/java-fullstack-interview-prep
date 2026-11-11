# Generics

## 1. Why generics?

Generics let classes, interfaces and methods work with types while preserving compile-time type safety.

```java
List<String> names = new ArrayList<>();
names.add("Maya");
// names.add(10); // compile-time error
String first = names.get(0);
```

Without generics, code often needs casts and can fail at runtime with ClassCastException.

## 2. Generic classes and methods

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

Common type parameter names: `T` (type), `E` (element), `K` (key), `V` (value), `N` (number).

## 3. Bounded type parameters

```java
static <T extends Number> double twice(T value) {
    return value.doubleValue() * 2;
}
```

`extends` is used for both class and interface bounds. Multiple bounds are possible, with a class (if present) first.

## 4. Wildcards and PECS

- `List<? extends Number>`: producer of Number values. You can safely read as Number, but cannot add a specific Number because the actual list might be List<Integer>.
- `List<? super Integer>`: consumer of Integer values. You can add Integer values, but reads are only safely typed as Object.
- `List<?>`: unknown element type; useful when the method does not need to add values.

**PECS**: Producer Extends, Consumer Super.

```java
static double sum(List<? extends Number> values) {
    double total = 0;
    for (Number n : values) total += n.doubleValue();
    return total;
}
static void addDefaults(List<? super Integer> target) {
    target.add(1);
    target.add(2);
}
```

## 5. Invariance

Even though Integer extends Number, `List<Integer>` is not a subtype of `List<Number>`. If it were, code could add a Double to a list intended for Integer values.

Wildcards provide controlled flexibility.

## 6. Type erasure and limitations

Java generics are mostly implemented through type erasure: generic type information is largely removed during compilation, with casts and bridge methods used where needed.

Consequences:
- You cannot write `new T()`.
- You cannot create `new T[10]`.
- You cannot use primitive types as type arguments; use wrappers.
- You generally cannot overload methods whose signatures erase to the same signature.
- At runtime, `List<String>` and `List<Integer>` have the same raw runtime class.

## 7. Interview questions

**What is PECS?** Use extends when reading from a producer, super when writing to a consumer.

**Why is List<Integer> not List<Number>?** Generic types are invariant to preserve type safety.

**What is type erasure?** The compiler implements generic types largely by removing type parameters and inserting casts/bridge methods.

**Can we use primitives in generics?** No; use wrapper types such as Integer.

## 8. Practice

Write a generic Box, a generic method, a bounded method, and a method that copies values from `List<? extends T>` to `List<? super T>`.
