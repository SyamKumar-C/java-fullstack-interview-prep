# Exception Handling

## 1. Exception hierarchy

`Throwable` is the root. Its main branches are:
- `Error`: serious JVM or environment problems; applications usually should not try to recover from these.
- `Exception`: conditions an application may handle.
  - Checked exceptions: must be caught or declared.
  - Runtime exceptions: unchecked; often indicate programming errors or invalid input.

Examples: `IOException` is checked; `NullPointerException` and `IllegalArgumentException` are unchecked.

## 2. try, catch and finally

```java
try {
    int result = 10 / Integer.parseInt("0");
} catch (NumberFormatException ex) {
    System.out.println("Input is not a number");
} catch (ArithmeticException ex) {
    System.out.println("Cannot divide by zero");
} finally {
    System.out.println("Cleanup");
}
```

Catch more specific exceptions before broader parent types. A `finally` block usually runs whether the try block completes normally or exits by throwing, but it is not an absolute guarantee if the JVM/process terminates.

## 3. throw vs throws

- `throw` actually throws an exception object.
- `throws` declares exceptions a method may pass to its caller.

```java
void validate(int age) {
    if (age < 18) throw new IllegalArgumentException("Underage");
}
void readFile() throws IOException {
    // file operation
}
```

## 4. try-with-resources

Use it for resources implementing `AutoCloseable`. Java closes them automatically, including when an exception occurs.

```java
try (var reader = java.nio.file.Files.newBufferedReader(
        java.nio.file.Path.of("input.txt"))) {
    System.out.println(reader.readLine());
}
```

This is preferred over manually closing resources in `finally`.

## 5. Custom exceptions

Create a domain-specific exception when it makes an error clearer to callers.

```java
class InsufficientBalanceException extends RuntimeException {
    InsufficientBalanceException(String message) {
        super(message);
    }
}
```

In Spring Boot, a service may throw a domain exception and a `@RestControllerAdvice` can translate it into a consistent HTTP response.

## 6. Best practices

- Do not swallow exceptions with an empty catch block.
- Catch exceptions you can meaningfully handle.
- Preserve the cause when wrapping: `throw new ServiceException("Failed", ex)`.
- Do not use exceptions for normal branching.
- Avoid logging the same exception at every layer.
- Do not expose stack traces or internal details in API responses.

## 7. Interview questions

**Checked vs unchecked?** Checked exceptions are enforced by the compiler; unchecked exceptions extend RuntimeException (or Error).

**Can finally fail to execute?** Yes, for example if the process exits or the JVM halts.

**Can we have try without catch?** Yes, with finally or try-with-resources.

**What is exception propagation?** An exception moves up the call stack until a matching handler catches it or the thread terminates.

## 8. Practice

Build a service method that validates an order, throws a custom exception for invalid state and maps it to HTTP 400 using Spring's exception handler.
