# REST API Design, OAuth 2.0 and Rate Limiting

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Short interview answer
A good REST API models resources, uses HTTP methods/status codes consistently, validates input, supports safe pagination, and returns predictable errors. Authentication verifies identity; authorization checks permissions. OAuth 2.0 is an authorization framework; OpenID Connect adds an identity layer.

## 2. Resource design

```text
GET    /api/orders?status=PAID&page=0&size=20
GET    /api/orders/42
POST   /api/orders
PATCH  /api/orders/42
DELETE /api/orders/42
```

Use nouns for resources, query parameters for filtering/sorting/pagination, and path variables for resource identity. Use request DTOs rather than binding JPA entities directly. Define an API versioning strategy when compatibility requires it.

POST is not idempotent by default. PUT is intended to replace a resource representation and is idempotent by HTTP semantics. PATCH applies partial changes; whether it is idempotent depends on the operation.

## 3. Pagination and errors
Offset pagination is simple, but deep OFFSET pages can become expensive. Keyset pagination uses a stable cursor such as (createdAt, id). Return consistent errors with a machine-readable code, safe message, timestamp, and trace ID. Do not expose stack traces, SQL, tokens, or internal hostnames.

## 4. OAuth 2.0 and OIDC
OAuth 2.0 lets a client obtain limited access to a resource server using tokens from an authorization server. Common flows:
- Authorization Code with PKCE: common for browser/mobile public clients.
- Client Credentials: service-to-service access when no user is acting.
- Refresh token: obtain a new access token under authorization-server rules.

OIDC adds identity claims and an ID token. Do not use an ID token as an API access token unless the API explicitly defines that behavior. JWT is a token format, not an authentication protocol. Validate signature, issuer, audience, expiry, and required claims. Do not trust a token merely because it can be decoded.

## 5. Rate limiting
- Fixed window: simple counter per time window; bursts can happen at boundaries.
- Sliding window: smoother count over a moving interval.
- Token bucket: tokens refill at a rate and allow controlled bursts.
- Leaky bucket: smooths outgoing request rate.

A multi-instance deployment needs shared state or gateway-level enforcement for a global limit. Return HTTP 429 and, where useful, Retry-After. Choose limit identity based on the threat model: user, API key, tenant, or IP.

## 6. Idempotency keys
For retryable payment/order creation, accept a client-generated idempotency key. Store it with request identity and result under a unique constraint. A retry with the same key returns the original outcome rather than creating a second order. Define expiry and reject reuse with a different payload.

## 7. Common mistakes
- Treating JWT and OAuth as synonyms.
- Using an ID token as an access token.
- Returning 200 for every outcome.
- Exposing internal exception details.
- Trusting client-supplied tenant IDs without authorization checks.
- Applying only in-memory rate limits across many instances.
- Retrying a non-idempotent POST without an idempotency design.

## 8. Interview questions
**Q1. Authentication vs authorization?** Authentication verifies identity; authorization checks permissions.

**Q2. OAuth 2.0 vs OIDC?** OAuth 2.0 is delegated authorization; OIDC adds identity and authentication information.

**Q3. Is JWT an authentication protocol?** No. JWT is a token format that can carry claims.

**Q4. Why use an idempotency key?** It makes retries safe when the client cannot tell whether the first request succeeded.

**Q5. What is HTTP 429?** Too Many Requests; the caller exceeded a rate limit.

## 9. Practice
1. Design endpoints for order creation, cancellation, and listing.
2. Explain why a payment POST needs idempotency protection.
3. Choose a rate-limit key for a multi-tenant API.
4. Explain access token vs ID token.
