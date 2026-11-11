# Spring REST APIs, Validation and Exception Handling

A common flow is Controller → Service → Repository. Controllers handle HTTP, services coordinate business rules and repositories handle persistence.

```java
@RestController
@RequestMapping("/orders")
class OrderController {
  private final OrderService service;
  OrderController(OrderService service) { this.service = service; }
  @PostMapping
  ResponseEntity<OrderResponse> create(@Valid @RequestBody CreateOrder request) {
    return ResponseEntity.status(HttpStatus.CREATED).body(service.create(request));
  }
}
```

Use Jakarta Validation annotations such as @NotNull, @NotBlank, @Positive, @Size and @Email. @Valid triggers request validation; business invariants still belong in service/domain logic.

Use @RestControllerAdvice and @ExceptionHandler to map known exceptions to stable responses. Never expose stack traces or SQL details.

Common statuses: 200, 201, 204, 400, 401, 403, 404, 409 and 500.

**Interview:** Where should validation happen? At request boundaries and in domain logic for business invariants.
