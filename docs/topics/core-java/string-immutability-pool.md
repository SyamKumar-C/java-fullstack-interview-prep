# String Immutability and String Pool in Java

## 1. String immutability

A String is immutable: once created, its character sequence cannot be changed. Operations such as concat, replace, and toUpperCase return a new String when the result differs.

```java
String value = "java";
value.concat(" spring");
System.out.println(value); // java

value = value.concat(" spring");
System.out.println(value); // java spring
```

The first concat does not modify the original object. The second assigns the returned String to the variable.

## 2. Why String is immutable

Immutability provides:
- Safe sharing across code and threads.
- Stable hash codes, useful for map keys.
- Security benefits when strings represent paths, class names, or configuration values.
- String pool reuse without one caller changing a shared value.

## 3. String pool

The JVM maintains a pool of interned strings. String literals with the same content can refer to the same pooled object.

```java
String a = "hello";
String b = "hello";
System.out.println(a == b); // true in this literal example
```

The pool is an optimization and canonicalization mechanism. Do not use `==` for general content comparison.

## 4. new String and intern()

```java
String a = "java";
String b = new String("java");
System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true

String c = b.intern();
System.out.println(a == c);      // true
```

`new String` creates a distinct String object. `intern()` returns the canonical pooled representation for equal content.

## 5. == versus equals

- `==` compares references for objects.
- `equals()` compares content for String.

Always use equals (or Objects.equals for null-safe comparison) when comparing string values.

## 6. StringBuilder and StringBuffer

| String | StringBuilder | StringBuffer |
|---|---|---|
| Immutable | Mutable | Mutable |
| Repeated concatenation creates results | Efficient repeated changes | Synchronized methods |
| Safe to share because immutable | Not thread-safe by default | Thread-safe at method level |

Use StringBuilder for repeated concatenation in a single thread. StringBuffer is less commonly needed; synchronization of individual methods does not automatically make a multi-call workflow atomic.

## 7. Common mistakes

- Using == to compare text.
- Expecting concat to mutate the original.
- Using String in a large loop for repeated concatenation.
- Assuming every string created at runtime is automatically interned.
- Calling intern indiscriminately on high-cardinality user input.

## 8. Interview questions and answers

**Q1. Why is String immutable?**  
It supports safe sharing, stable hashing, security, and string-pool reuse.

**Q2. Difference between == and equals for String?**  
== compares references; equals compares character content.

**Q3. What is the String pool?**  
A JVM-managed pool of canonical string instances that allows equal literals/interned strings to share an object.

**Q4. What does intern() do?**  
It returns the canonical pooled String for the same content.

**Q5. StringBuilder versus StringBuffer?**  
Both are mutable. StringBuffer synchronizes its methods; StringBuilder is generally faster for single-threaded construction.

**Q6. Why is String a good HashMap key?**  
It is immutable, so its equality and hash code remain stable after insertion.

## 9. Practice

Predict the output of comparisons involving literals, new String, concatenation at compile time, and concatenation at runtime. Explain each result using reference equality versus content equality.

## 10. Quick revision

String is immutable. == compares references; equals compares content. Literals can share pooled instances. Use StringBuilder for repeated construction.
