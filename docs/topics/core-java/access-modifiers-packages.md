# Java Access Modifiers and Packages

## 1. Why access control matters

Access modifiers define where a class, constructor, field, or method can be used. They protect implementation details and help define a stable public API.

## 2. Four access levels

| Modifier | Same class | Same package | Subclass in another package | Unrelated other package |
|---|---|---|---|---|
| private | Yes | No | No | No |
| package-private (no modifier) | Yes | Yes | No, except inherited visibility nuances | No |
| protected | Yes | Yes | Yes, through subclass access rules | No |
| public | Yes | Yes | Yes | Yes |

A top-level class can be public or package-private; it cannot be private or protected. Nested classes can use all four access levels.

## 3. protected across packages

Protected access outside the package is available to subclasses, but it is not simply public access. In a subclass in another package, access through a qualifying subclass reference is restricted; arbitrary access through a parent-typed reference is not allowed.

This rule prevents protected from becoming a general cross-package public API.

## 4. Packages

A package groups related types and provides a namespace. It also participates in access control.

```java
package com.example.order.service;

import com.example.order.model.Order;
```

Use clear package boundaries such as controller, service, repository, domain, and configuration where they help organize the application. Avoid treating package structure as a substitute for proper module boundaries.

## 5. Imports and fully qualified names

An import lets code use a type's simple name. It does not copy the class into the current package. Two types with the same simple name can be referenced using fully qualified names.

Static imports allow direct access to static members, but excessive static imports can make code harder to read.

## 6. Common mistakes

- Thinking package-private is the same as protected.
- Assuming protected is accessible to every class in another package.
- Declaring every field public.
- Confusing package names with folder names at runtime (build tools compile package paths, but package identity is part of the class name).
- Assuming import changes access permissions.

## 7. Interview questions and answers

**Q1. What are the four access modifiers?**  
private, package-private (no modifier), protected, and public.

**Q2. Difference between protected and package-private?**  
Package-private is available only within the same package. Protected is also available to subclasses outside the package under specific access rules.

**Q3. Can a top-level class be private?**  
No. A top-level class can be public or package-private.

**Q4. Does import make a private class accessible?**  
No. Imports only resolve names; they do not change visibility.

**Q5. Why keep fields private?**  
To protect invariants and expose controlled behavior rather than allowing arbitrary external mutation.

## 8. Practice

Create two packages. Define a class with private, package-private, protected, and public members. Test access from a same-package class, unrelated class in another package, and subclass in another package.

## 9. Quick revision

private = class only. package-private = package. protected = package plus subclass access. public = everywhere. Imports do not change visibility.
