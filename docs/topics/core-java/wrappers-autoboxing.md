# Wrapper Classes, Autoboxing and Unboxing

## 1. Primitive and wrapper types

Java primitives include `int`, `long`, `double`, `boolean` and others. Wrapper classes represent primitive values as objects: `Integer`, `Long`, `Double`, `Boolean`, etc.

Collections store objects, not primitive values, so a `List<Integer>` is used instead of `List<int>`.

## 2. Autoboxing and unboxing

Autoboxing converts a primitive to its wrapper. Unboxing converts a wrapper to its primitive.

```java
Integer count = 10; // autoboxing
int value = count; // unboxing
```

Conceptually, the compiler inserts calls such as `Integer.valueOf(10)` and `count.intValue()`.

## 3. Null unboxing

```java
Integer count = null;
int value = count; // NullPointerException
```

Check for null before unboxing when values may be absent, for example when reading nullable database columns.

## 4. Wrapper caching and ==

Some wrapper factory methods cache values. For Integer, the required cache range includes -128 to 127; implementations may cache more.

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b); // typically true due to required cache

Integer x = 1000;
Integer y = 1000;
System.out.println(x == y); // do not rely on this being true
System.out.println(x.equals(y)); // true
```

Use `equals()` to compare wrapper values, not `==`.

## 5. Performance considerations

Repeated boxing can create objects or add overhead, especially in large loops and streams. Primitive-specialized APIs such as `IntStream`, `LongStream` and `mapToInt()` can avoid unnecessary boxing.

## 6. Interview questions

**Why can't List store int?** Java generics work with reference types, not primitive types.

**What is unboxing a null wrapper?** It throws NullPointerException.

**Is Integer immutable?** Yes; wrapper instances are immutable value objects.

**How should wrappers be compared?** Use `equals()` or safely unbox after null checks.

## 7. Practice

Write a program that demonstrates autoboxing, unboxing, null unboxing and wrapper comparison. Explain why `==` can produce surprising results.
