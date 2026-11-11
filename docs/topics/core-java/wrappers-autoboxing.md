# Wrapper Classes, Autoboxing and Unboxing

## 1. Primitive and wrapper types

Java primitives include int, long, double and boolean. Wrapper classes such as Integer, Long, Double and Boolean represent primitive values as objects. Collections and generic type parameters require reference types, so use List<Integer>, not List<int>.

## 2. Autoboxing and unboxing

Autoboxing converts a primitive to a wrapper; unboxing converts a wrapper to a primitive.

```java
Integer count = 10; // autoboxing
int value = count;  // unboxing
```

The compiler conceptually inserts calls such as Integer.valueOf() and intValue().

## 3. Null unboxing

```java
Integer count = null;
int value = count; // NullPointerException
```

Check nullable values before unboxing, especially values from database columns or API mappings.

## 4. Wrapper cache and ==

Integer factory methods cache at least values from -128 to 127. Implementations may cache more.

```java
Integer a = 100, b = 100;
System.out.println(a == b); // true due to cache
Integer x = 1000, y = 1000;
System.out.println(x == y); // do not rely on this
System.out.println(x.equals(y)); // true
```

Use equals() for value comparison, not ==.

## 5. Performance

Repeated boxing may add allocation/overhead. Primitive-specialized APIs such as IntStream, LongStream and mapToInt() can avoid unnecessary boxing.

## 6. Interview questions

**Why can't List store int?** Generics use reference types, not primitives.

**What happens when null is unboxed?** NullPointerException.

**Are wrappers immutable?** Yes.

**How should wrappers be compared?** equals(), after handling null.

## 7. Practice

Demonstrate autoboxing, unboxing, null unboxing and Integer comparison. Explain why == can be surprising.
