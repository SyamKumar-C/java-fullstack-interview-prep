# Spring Boot REST APIs, Validation and Exception Handling

## 1. REST controller basics

A REST controller exposes HTTP endpoints. `@RestController` combines controller semantics with response-body serialization.

```java
@RestController
@RequestMapping("/api/orders")
class OrderController {
    private final OrderService service;

    OrderController(OrderService service) {
        this.service = service;
    }

    @GetMapping("/{id}")
    OrderResponse get(@PathVariable long id) {
        return service.get(id);
    }
}
```

Keep controllers focused on HTTP concerns: request binding, validation, response status, and delegation. Business rules belong in the service/domain layer.

## 2. Request mapping annotations

- @GetMapping: retrieve a resource.
- @PostMapping: create or trigger processing.
- @PutMapping: replace/update a resource representation.
- @PatchMapping: partial update.
- @DeleteMapping: delete a resource.

Use @PathVariable for resource identity, @RequestParam for query parameters, and @RequestBody for a request body.

## 3. DTOs

Use request/response DTOs rather than exposing JPA entities directly. DTOs control the API contract, avoid accidental lazy loading, and prevent clients from setting internal fields such as status or tenant identifiers without authorization.

## 4. Bean Validation

```java
record CreateOrderRequest(
    @NotBlank String customerCode,
    @NotEmpty List<@Valid OrderLineRequest> lines
) {}

@PostMapping
ResponseEntity<OrderResponse> create(
    @Valid @RequestBody CreateOrderRequest request) {
    return ResponseEntity.status(HttpStatus.CREATED).body(service.create(request));
}
```

Common constraints include NotNull, NotBlank, NotEmpty, Size, Min, Max, Email, and Pattern. Use class-level/custom validators for cross-field rules. Validation checks input shape; business rules still belong in the service/domain layer.

## 5. Exception handling

Use `@RestControllerAdvice` with `@ExceptionHandler` to map known exceptions to consistent HTTP responses. Use ProblemDetail where supported by the Spring version.

Return safe messages and stable error codes. Do not expose stack traces, SQL, internal IPs, or secrets to clients.

## 6. Status code choices

- 200 OK: successful request with response.
- 201 Created: resource created; Location header can identify it.
- 202 Accepted: accepted for asynchronous processing.
- 204 No Content: successful with no response body.
- 400 Bad Request: invalid request syntax/validation.
- 401 Unauthorized: missing/invalid authentication.
- 403 Forbidden: authenticated but not allowed.
- 404 Not Found: resource absent.
- 409 Conflict: state conflict such as duplicate or version conflict.
- 422 Unprocessable Content: syntactically valid but semantically invalid request, when used by the API contract.
- 500 Internal Server Error: unexpected server failure.

## 7. Pagination and sorting

Use Spring Data Pageable or explicit page/size/sort parameters. Validate maximum page size to prevent clients from requesting huge result sets. Return a stable response contract with content and pagination metadata.

## 8. Common mistakes

- Returning entities directly.
- Putting business logic in controllers.
- Returning 200 for every outcome.
- Exposing exception stack traces.
- Forgetting nested @Valid validation.
- Allowing unbounded page sizes.
- Trusting client-supplied owner/tenant IDs without authorization.

## 9. Interview questions and answers

**Q1. @Controller versus @RestController?**  
@RestController is a convenience annotation combining @Controller and @ResponseBody, so handler return values are written to the response body by default.

**Q2. PathVariable versus RequestParam?**  
PathVariable identifies a path segment/resource. RequestParam reads query parameters.

**Q3. How does @Valid work?**  
It triggers Bean Validation on the bound object and can validate nested objects when nested fields use @Valid.

**Q4. Why use DTOs?**  
They separate the external API contract from persistence entities and prevent overexposure of internal state.

**Q5. Where should business validation happen?**  
Input-shape validation can happen at the API boundary. Domain/business invariants must also be enforced in the service/domain layer so other entry points cannot bypass them.

**Q6. 401 versus 403?**  
401 means authentication is missing or invalid. 403 means the caller is authenticated but lacks permission.

## 10. Practice

Create POST /orders with nested line validation. Map invalid input to 400, missing order to 404, and duplicate order reference to 409. Return a DTO rather than an entity.

## 11. Quick revision

Controller handles HTTP; service owns business logic. Validate DTOs at the boundary, enforce domain rules in the domain/service, and return consistent safe errors.
