# Exception Handling in Java

## 1. What is an exception?

An exception is an event that interrupts the normal flow of a program. Java represents exceptions as objects in the `Throwable` hierarchy.

```
Throwable
├── Error
└── Exception
    └── RuntimeException
```

- `Error`: serious JVM or environment problems; applications generally should not try to recover from them.
- Checked exception: an Exception subtype that is not a RuntimeException. The compiler requires handling or declaration.
- Unchecked exception: RuntimeException and its subclasses. No compile-time handling requirement.

## 2. Checked versus unchecked

| Checked | Unchecked |
|---|---|
| Checked by compiler | Not required to be caught or declared |
| Often represents a recoverable external condition | Often represents invalid input, state, or programming errors |
| Example: IOException, SQLException | Example: NullPointerException, IllegalArgumentException |

Choose based on the API contract, not a rule that every business error must be checked. In Spring applications, unchecked domain exceptions are common and can be mapped to HTTP responses.

## 3. try, catch, finally, throw and throws

```java
static String readName(Path path) throws IOException {
    try {
        return Files.readString(path);
    } catch (AccessDeniedException ex) {
        throw new IOException("Cannot access file: " + path, ex);
    } finally {
        System.out.println("Read attempt finished");
    }
}
```

- `try`: code that may fail.
- `catch`: handles a matching exception.
- `finally`: cleanup code that normally runs whether the try succeeds or fails.
- `throw`: actually throws an exception object.
- `throws`: declares possible exceptions in a method signature.

A finally block may not run if the JVM exits abruptly, the process is killed, or the machine loses power. Avoid returning from finally because it can hide an earlier return or exception.

## 4. try-with-resources

Use it for resources implementing AutoCloseable. Java closes them automatically, including when an exception occurs.

```java
try (var reader = Files.newBufferedReader(path)) {
    return reader.readLine();
}
```

If both the body and close operation fail, the body exception is primary and close failures are available as suppressed exceptions.

## 5. Custom exceptions

Create domain-specific exceptions when they make the failure meaningful to callers.

```java
class InsufficientBalanceException extends RuntimeException {
    InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Do not create a large exception class for every minor condition. Preserve the original cause when translating an exception:

```java
throw new PaymentFailedException("Payment provider failed", cause);
```

## 6. Exception propagation

If a method does not catch an exception, it propagates up the call stack. The first compatible catch block handles it. If no handler exists, the thread's uncaught-exception handler is invoked.

Do not catch an exception and silently ignore it. Either recover, add useful context and rethrow, or let it propagate.

## 7. Spring Boot scenario

A REST API can translate domain exceptions into consistent HTTP responses using `@RestControllerAdvice` and `@ExceptionHandler`.

```java
@RestControllerAdvice
class ApiExceptionHandler {
    @ExceptionHandler(InsufficientBalanceException.class)
    ResponseEntity<ProblemDetail> handle(InsufficientBalanceException ex) {
        ProblemDetail problem =
            ProblemDetail.forStatusAndDetail(HttpStatus.CONFLICT, ex.getMessage());
        return ResponseEntity.status(HttpStatus.CONFLICT).body(problem);
    }
}
```

Avoid returning stack traces, SQL details, tokens, or internal hostnames to clients. Log technical details safely and return a stable error response.

## 8. Best practices

- Catch the narrowest useful exception type.
- Do not use exceptions for normal branching.
- Preserve causes when wrapping.
- Include context that helps diagnose the failure.
- Do not log and rethrow at every layer; this creates duplicate logs.
- Close resources with try-with-resources.
- Do not expose sensitive information in exception messages.
- Define clear retry rules; not every exception is retryable.

## 9. Interview questions and answers

**Q1. Difference between throw and throws?**  
`throw` raises an exception at a particular point. `throws` declares exceptions a method may pass to its caller.

**Q2. Does finally always execute?**  
It executes in normal control flow, including when an exception is thrown or a return is attempted. It is not guaranteed after abrupt JVM/process termination.

**Q3. Can we have try without catch?**  
Yes, if it has a finally block, or if it is a try-with-resources statement.

**Q4. Can a catch block catch multiple exception types?**  
Yes, with multi-catch: `catch (IOException | SQLException ex)`. The alternatives cannot have a subclass/superclass relationship.

**Q5. What are suppressed exceptions?**  
They are secondary exceptions, commonly thrown while closing resources, attached to the primary exception. Read them with `getSuppressed()`.

**Q6. Should business exceptions be checked or unchecked?**  
Choose based on the caller contract. Unchecked exceptions are common in service layers because Spring transaction rollback defaults are aligned with them, but checked exceptions can be useful when callers are expected to recover explicitly.

## 10. Coding exercises

1. Read a file using try-with-resources and handle a missing file.
2. Create a custom InvalidOrderException and map it to HTTP 400.
3. Write a method that translates SQLException into a repository-specific exception while preserving the cause.

## 11. Quick revision

- Checked: compiler enforces handling/declaration.
- Unchecked: RuntimeException family.
- throw raises; throws declares.
- finally is for cleanup; try-with-resources is preferred for closeable resources.
