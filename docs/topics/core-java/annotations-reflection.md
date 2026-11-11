# Java Annotations and Reflection

## 1. Annotations

Annotations add metadata to code. They can be read by the compiler, build tools or runtime frameworks. An annotation does not automatically change behavior; a compiler, processor or framework must interpret it.

Common built-in annotations:
- `@Override`: compiler checks that a method overrides a parent method.
- `@Deprecated`: marks an API as discouraged.
- `@SuppressWarnings`: suppresses selected compiler warnings.
- `@FunctionalInterface`: checks that an interface has one abstract method.

Spring uses annotations such as `@Service`, `@Autowired`, `@Transactional` and `@RestController` as metadata for framework behavior.

## 2. Creating a custom annotation

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Audited {
    String action();
}
```

- Retention: SOURCE, CLASS or RUNTIME.
- Target: where the annotation may be used (method, type, field, parameter, etc.).
- Documented: include in generated Javadoc.
- Inherited: class-level annotation inheritance behavior; it does not apply to every annotation location.

## 3. Reflection

Reflection lets code inspect classes, constructors, fields, methods and annotations at runtime.

```java
Class<?> type = User.class;
for (Method method : type.getDeclaredMethods()) {
    System.out.println(method.getName());
}
```

Reflection can also invoke methods or access fields, subject to access checks and module boundaries.

## 4. Practical uses

- Dependency injection frameworks inspect constructors and annotations.
- Serialization libraries inspect fields/getters.
- Test frameworks discover test methods.
- A plugin system loads a class by name and checks whether it implements an expected interface.

## 5. Costs and risks

Reflection can make code harder to understand, reduce compile-time safety and complicate refactoring. Deep reflective access can be restricted by the Java module system. Prefer normal interfaces and direct calls unless dynamic inspection is needed.

## 6. Annotation processing vs reflection

Annotation processing runs during compilation and can generate source files or validate declarations. Reflection runs at runtime and inspects loaded classes. They solve different problems.

## 7. Common mistakes

- Assuming annotations execute behavior by themselves.
- Using SOURCE retention when runtime inspection is required.
- Using getDeclaredMethods and expecting inherited methods too.
- Ignoring module/access restrictions.
- Replacing simple polymorphism with unnecessary reflection.

## 8. Interview questions

**Q1. What is an annotation?** Metadata attached to program elements and interpreted by tools, compilers or frameworks.

**Q2. RetentionPolicy.RUNTIME vs CLASS?** RUNTIME is available through reflection; CLASS is stored in the class file but not required to be available at runtime reflection.

**Q3. What is reflection?** Runtime inspection and interaction with classes and members.

**Q4. Annotation processor vs reflection?** Processor works during compilation; reflection works at runtime.

**Q5. Does @Transactional work because it is an annotation?** No. Spring infrastructure interprets it and applies transaction advice, commonly through a proxy.

## 9. Practice

Create a runtime method annotation and write a small utility that scans a class and prints the action value for annotated methods.
