# REST APIs and Security

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 2–3 hours

A REST-style API exposes resources through a consistent interface, commonly using HTTP methods and status codes.

## 1. HTTP methods and status codes

- GET retrieves a resource and should be safe and idempotent.
- POST commonly creates a resource or triggers processing; it is not inherently idempotent.
- PUT replaces a resource representation and is idempotent by definition.
- PATCH applies a partial update; idempotency depends on the patch semantics.
- DELETE requests removal and is defined as idempotent in HTTP semantics.

Common status codes: 200 OK, 201 Created, 202 Accepted, 204 No Content, 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Content, and 500 Internal Server Error.

## 2. Idempotency and retries

An operation is idempotent when repeating the same request has the same intended effect as making it once.

For payment or order creation, an idempotency key can help prevent duplicate processing. Store the key with the request outcome and define expiry and conflict behavior. A retry policy should use timeouts, bounded attempts, and backoff.

## 3. API design

- Use resource-oriented URLs.
- Validate request data at the boundary.
- Use DTOs rather than exposing persistence entities.
- Provide consistent error responses.
- Add pagination, filtering, sorting, and versioning where needed.
- Avoid leaking internal exception details.

## 4. Authentication vs authorization

Authentication establishes who the caller is. Authorization determines what that caller may do.

## 5. Sessions, JWT, OAuth 2.0, and OIDC

- Session authentication stores session state on the server and usually sends a session identifier in a cookie.
- JWT is a token format; a signed JWT is not automatically encrypted.
- OAuth 2.0 is an authorization framework for delegated access.
- OpenID Connect adds an identity layer on top of OAuth 2.0.

Validate token signature, issuer, audience, expiry, and required claims. Plan refresh, revocation, key rotation, and secure storage.

## 6. CORS and CSRF

CORS is a browser policy controlling cross-origin access to responses. It is not authentication and does not protect an API from non-browser clients.

CSRF is a risk when browsers automatically attach credentials such as cookies. Use appropriate CSRF defenses for cookie-based authentication. Bearer tokens explicitly attached by application code have a different CSRF risk profile, but still need secure storage and XSS protection.

## 7. Passwords, secrets, and rate limits

Store passwords using a dedicated password-hashing algorithm such as Argon2id, bcrypt, or scrypt with appropriate parameters and salts. Do not store plain text or use a fast general-purpose hash alone.

Keep API keys and credentials in a secret manager or secure environment configuration. Rate limiting can protect expensive or abuse-prone endpoints.

## 8. Interview questions

??? question "What is the difference between authentication and authorization?"
    Authentication verifies identity. Authorization checks whether that identity has permission to perform an action or access a resource.

??? question "Is JWT encrypted?"
    Not necessarily. A typical signed JWT protects integrity and authenticity, but its claims are readable by anyone who has the token. Use encryption only when confidentiality is required and supported.

??? question "What is CORS?"
    CORS is a browser-enforced mechanism that controls whether JavaScript from one origin may access a response from another origin. It is not a substitute for server-side authentication or authorization.

??? question "How do you prevent duplicate payment requests?"
    Use an idempotency key, persist the key and outcome safely, and ensure concurrent requests with the same key cannot create multiple charges. Also use provider-side idempotency where available.

??? question "What is the difference between 401 and 403?"
    401 means valid authentication credentials are missing or invalid. 403 means the server understood the request but refuses to authorize it.

## 9. Practice

1. Design a paginated GET endpoint.
2. Design an idempotent order-creation endpoint.
3. Explain JWT validation and refresh-token handling.
4. Explain why CORS does not secure an API from direct HTTP clients.
