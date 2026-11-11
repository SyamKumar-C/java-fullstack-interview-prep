# HTTP Methods and Status Codes

## 1. HTTP request structure

An HTTP request has a method, target URL, headers and optional body. The response has a status code, headers and optional body. HTTP is stateless at the protocol level; applications use tokens, cookies or other mechanisms to maintain identity.

## 2. Common methods

| Method | Meaning | Safe? | Idempotent? |
|---|---|---:|---:|
| GET | Read resource | Yes | Yes |
| HEAD | GET metadata without response body | Yes | Yes |
| POST | Submit/process/create under a collection | No | Not by default |
| PUT | Replace resource at target URI | No | Yes by semantics |
| PATCH | Partial modification | No | Depends on patch |
| DELETE | Remove resource | No | Yes by semantics |

Safe means intended not to change server state. Idempotent means repeating the same request has the same intended effect as one request. An idempotent request can still return a different response.

## 3. Common status code groups

- 1xx: informational.
- 2xx: success.
- 3xx: redirection.
- 4xx: client/request problem.
- 5xx: server/dependency problem.

Frequently used codes:
- 200 OK: successful request.
- 201 Created: resource created; Location may identify it.
- 202 Accepted: accepted for asynchronous processing, not completed yet.
- 204 No Content: success with no response body.
- 400 Bad Request: invalid request.
- 401 Unauthorized: authentication is missing/invalid.
- 403 Forbidden: authenticated caller is not allowed.
- 404 Not Found: resource not found or intentionally hidden.
- 409 Conflict: state conflict.
- 422 Unprocessable Content: syntactically valid but semantically invalid content (API convention).
- 429 Too Many Requests: rate limit.
- 500 Internal Server Error: unexpected server error.
- 502/503/504: gateway/dependency failures, unavailable service or timeout.

## 4. PUT vs PATCH

PUT generally replaces the target resource representation and is idempotent by intended semantics. PATCH applies a partial change; whether it is idempotent depends on the patch operation.

## 5. API design example

```
GET    /api/orders/42
POST   /api/orders
PATCH  /api/orders/42
DELETE /api/orders/42
```

Use plural resource names and model resources rather than exposing implementation methods in URLs.

## 6. Common mistakes

- Returning 200 for every outcome.
- Using GET for state-changing operations.
- Returning 201 without identifying the created resource where useful.
- Confusing 401 with 403.
- Assuming every PATCH is non-idempotent.
- Returning internal stack traces to clients.

## 7. Interview questions

**Q1. PUT vs PATCH?** PUT replaces a resource representation; PATCH applies a partial modification.

**Q2. 401 vs 403?** 401 means valid authentication is absent; 403 means the caller is not permitted.

**Q3. 200 vs 201?** 200 indicates success; 201 indicates a resource was created.

**Q4. Is DELETE idempotent?** By HTTP semantics, yes: repeating it has the same intended final effect, even if response codes differ.

**Q5. What is 202?** The request was accepted for processing, but processing may not be complete.

## 8. Practice

Design status codes for create order, invalid address, duplicate order key, unauthorized access, insufficient stock and asynchronous report generation.
