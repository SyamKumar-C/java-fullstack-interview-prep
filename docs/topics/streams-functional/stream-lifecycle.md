# Stream Lifecycle and Pipeline

A Stream is a sequence of elements supporting aggregate operations; it is not a data structure. A pipeline has a source, zero or more intermediate operations, and a terminal operation.

```java
List<String> result = names.stream()
  .filter(n -> n.length() > 3)
  .map(String::toUpperCase)
  .toList();
```

Intermediate operations are lazy; terminal operations trigger execution. A stream is consumed once. Create a new stream for another traversal.

map/filter are usually stateless. sorted/distinct are stateful and may buffer. Avoid side effects and shared mutable state inside pipelines, especially parallel streams.

**Interview:** Are streams reusable? No. What is laziness? Intermediate operations describe work; terminal operations trigger it.
