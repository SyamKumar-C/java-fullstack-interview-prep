# Java Initialization Order

## Order for first object creation
1. Superclass static fields/blocks, in textual order.
2. Subclass static fields/blocks, in textual order.
3. Superclass instance fields/blocks, in textual order.
4. Superclass constructor body.
5. Subclass instance fields/blocks, in textual order.
6. Subclass constructor body.

Static initialization happens once per class initialization; instance initialization happens for each object.

```java
class Parent {
  static { System.out.println("Parent static"); }
  { System.out.println("Parent instance"); }
  Parent() { System.out.println("Parent constructor"); }
}
class Child extends Parent {
  static { System.out.println("Child static"); }
  { System.out.println("Child instance"); }
  Child() { System.out.println("Child constructor"); }
}
```

First creation prints Parent static, Child static, Parent instance, Parent constructor, Child instance, Child constructor.

## Pitfalls
Avoid calling overridable methods from constructors. Dynamic dispatch can execute child code before child fields have been initialized.

**Interview:** Why is calling an overridable method in a constructor risky? The child override may observe partially initialized state.
