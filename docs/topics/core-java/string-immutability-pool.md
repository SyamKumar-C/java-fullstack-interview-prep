# String Immutability and String Pool

## 1. Why is String immutable?

A `String` object cannot be changed after it is created. Operations such as `concat()` return a new String rather than modifying the original.

```java
String name = "Java";
name.concat(" 21");
System.out.println(name); // Java
name = name.concat(" 21");
System.out.println(name); // Java 21
```

Immutability makes strings safe to share, supports string pooling, and makes their hash codes stable. It is useful for values such as configuration keys and map keys.

## 2. String pool

Java maintains a pool of canonical strings. String literals with the same contents can refer to the same pooled object.

```java
String a = "spring";
String b = "spring";
String c = new String("spring");

System.out.println(a == b);      // true
System.out.println(a == c);      // false
System.out.println(a.equals(c)); // true
```

- `==` compares references for objects.
- `equals()` compares String contents.
- `new String(...)` explicitly creates a distinct String object (unless optimized under rules that preserve observable behavior).

## 3. intern()

`intern()` returns the pooled canonical representation for the same string content.

```java
String x = new String("api");
String y = x.intern();
System.out.println(y == "api"); // true
```

Do not use `intern()` blindly for unbounded user input; pooling large numbers of unique strings can create memory pressure.

## 4. StringBuilder vs StringBuffer

| Type | Mutable | Thread-safe | Typical use |
|---|---|---|---|
| String | No | Safe to share because immutable | Text values |
| StringBuilder | Yes | No | Building text in one thread |
| StringBuffer | Yes | Synchronized methods | Legacy shared mutable text |

For repeated concatenation in a loop, use `StringBuilder` rather than creating many intermediate strings.

```java
StringBuilder result = new StringBuilder();
for (int i = 1; i <= 3; i++) result.append(i);
System.out.println(result); // 123
```

The compiler can optimize simple concatenation expressions, but explicit StringBuilder is useful for loops and incremental building.

## 5. Interview questions

**Why is String final/immutable?** Immutability supports safe sharing, stable hashing and security-sensitive use. The exact class design and implementation details are JVM/library concerns; the key contract is that String content cannot change.

**Difference between == and equals()?** Reference identity vs content equality.

**When would you use StringBuilder?** When repeatedly modifying or assembling text, especially in loops.

**Does StringBuilder guarantee thread safety?** No.

## 6. Practice

Predict the output of expressions using literals, `new String()`, `intern()`, `==` and `equals()`. Explain each comparison in terms of references and content.
