# Annotations and Reflection

## Annotations
Annotations attach metadata read by compilers, tools or runtime frameworks. Examples: `@Override`, `@Deprecated`, `@SuppressWarnings`, `@FunctionalInterface`.

## Custom annotation
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Audited { }
```
- `@Target` controls where it can be used.
- `@Retention` is SOURCE, CLASS or RUNTIME.
- `@Inherited` affects class annotations inherited by subclasses; it does not apply to methods.

## Reflection
Reflection inspects classes, methods, fields and constructors at runtime.
```java
for (var method : String.class.getDeclaredMethods()) {
  System.out.println(method.getName());
}
```
Frameworks use reflection for dependency injection, serialization and mapping. It adds complexity and can be restricted by module boundaries.

**Interview:** getMethods() returns public inherited methods; getDeclaredMethods() returns methods declared directly on the class, including non-public ones.
