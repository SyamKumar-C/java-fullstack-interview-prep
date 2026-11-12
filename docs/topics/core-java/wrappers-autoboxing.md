# Wrapper Classes, Autoboxing and Unboxing

## 1. Primitive types and wrappers

Java has eight primitive types: byte, short, int, long, float, double, char and boolean. Their wrapper classes are Byte, Short, Integer, Long, Float, Double, Character and Boolean.

Wrappers are objects, so they can be used where a reference type is required. Java generics do not accept primitives: use `List<Integer>`, not `List<int>`.

Wrapper objects are immutable.

## 2. Autoboxing and unboxing

- **Autoboxing:** primitive to wrapper, such as `int` to `Integer`.
- **Unboxing:** wrapper to primitive, such as `Integer` to `int`.

```java
Integer count = 10; // conceptually Integer.valueOf(10)
int value = count;  // conceptually count.intValue()
```

The compiler inserts conversions. They are convenient but can hide object creation and null risks.

## 3. Null unboxing causes NullPointerException

```java
Integer quantity = null;
int total = quantity; // NullPointerException
```

This commonly occurs when a nullable database column or API field is assigned to a primitive. Check for null or provide an intentional default before unboxing.

```java
int total = quantity == null ? 0 : quantity;
```

Do not use a default blindly if null has a different business meaning from zero.

## 4. Wrapper cache and ==

The Java language/API guarantees caching for certain wrapper factory values; Integer.valueOf caches at least -128 through 127. Implementations may cache additional values.

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b);      // true for cached values

Integer x = 1000;
Integer y = 1000;
System.out.println(x == y);      // do not rely on this
System.out.println(x.equals(y)); // true
```

For wrappers, `==` compares references. Use `equals()` for value comparison, and use `Objects.equals(a, b)` when either value may be null.

Never write application logic that depends on wrapper caching.

## 5. Numeric comparison and promotion

When wrappers are unboxed in arithmetic expressions, Java applies primitive numeric promotion. Mixed numeric wrapper types can therefore produce a wider primitive result.

For example, `Integer + Long` is evaluated using primitive numeric promotion after unboxing. Be careful with null operands because unboxing can throw.

## 6. Performance and collections

Repeated boxing/unboxing can add overhead and may create objects outside cached ranges. For large numeric stream operations, primitive streams such as `IntStream`, `LongStream` and `mapToInt` avoid much of the boxing cost.

Use primitive fields for mandatory numeric values. Use wrappers when nullability or generic/reference APIs require them.

## 7. Wrapper versus primitive

| Primitive | Wrapper |
|---|---|
| Holds a primitive value | Object reference to wrapper object |
| Cannot be null | Can be null |
| Usually lower memory/processing overhead | Object and reference overhead |
| Cannot be used as generic type argument | Can be used in collections/generics |
| Compare values with primitive operators | Use equals for value comparison |

## 8. Interview questions and answers

**Q1. Why can we not write List<int>?**  
Java generics require reference types. Use `List<Integer>`.

**Q2. What happens when a null Integer is assigned to int?**  
Unboxing calls the wrapper's primitive conversion and throws NullPointerException.

**Q3. Are wrapper classes mutable?**  
No. Wrapper instances are immutable.

**Q4. Why can Integer == return true for 100 but not be reliable for 1000?**  
Small values are cached by Integer.valueOf; object identity outside the guaranteed cache is not a value-comparison contract.

**Q5. How should nullable wrappers be compared?**  
Use `Objects.equals(a, b)` or explicitly handle null, depending on the requirement.

**Q6. When should we prefer primitives?**  
For mandatory numeric/boolean values and performance-sensitive calculations where null is not meaningful.

## 9. Practice

1. Compare two Integer objects inside and outside the guaranteed cache range.
2. Unbox a null Integer and explain the exception.
3. Add Integer and Long values and identify the resulting primitive type.
4. Compare a loop using `List<Integer>` with an `IntStream`; explain boxing.
