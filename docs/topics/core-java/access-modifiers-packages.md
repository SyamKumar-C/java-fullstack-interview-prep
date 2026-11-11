# Access Modifiers and Packages

## 1. Four access levels

| Modifier | Same class | Same package | Subclass in another package | Unrelated class elsewhere |
|---|---:|---:|---:|---:|
| private | Yes | No | No | No |
| package-private (no keyword) | Yes | Yes | No, except inherited access nuances | No |
| protected | Yes | Yes | Yes, through subclass access rules | No |
| public | Yes | Yes | Yes | Yes |

A top-level class can be `public` or package-private. It cannot be `private` or `protected`.

## 2. Protected access detail

Outside the declaring package, a subclass can access a protected member through inheritance, subject to Java's qualifying-reference rule. In simple terms, code in a subclass in another package cannot use a parent protected member through an arbitrary parent-class object.

## 3. Packages

Packages group related types, prevent naming collisions and help control access.

```java
package com.example.orders;

import java.util.List;

public class OrderService {
    public List<String> statuses() {
        return List.of("NEW", "PAID");
    }
}
```

The package declaration must appear before imports and type declarations. The folder structure conventionally mirrors the package name.

## 4. Encapsulation

Keep fields private and expose operations that protect valid state.

```java
public class BankAccount {
    private long balance;

    public void deposit(long amount) {
        if (amount <= 0) throw new IllegalArgumentException();
        balance += amount;
    }

    public long balance() { return balance; }
}
```

A public setter for every field is not automatically good encapsulation. Prefer methods that express valid business actions.

## 5. Interview questions

**What is the default access modifier?** If no access modifier is written, the member has package-private access.

**Can protected be accessed from another package?** Yes, from subclass code, subject to the protected qualifying-reference rule.

**Can a private method be overridden?** No; it is not inherited.

**Why use packages?** To organize code, avoid name collisions and control package-level access.

## 6. Practice

Create two packages. Try accessing a public, protected, package-private and private field from a class in the second package. Record which statements compile and why.
