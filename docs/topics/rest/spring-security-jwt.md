# Spring Security and JWT

Authentication verifies identity; authorization decides permitted actions.

## JWT flow
1. User authenticates.
2. Identity provider issues signed token.
3. Client sends Authorization: Bearer token.
4. Resource server validates signature, expiry, issuer and audience.
5. Claims/scopes/roles map to authorities.
6. Authorization rules decide access.

A signed JWT is not encrypted by default. Do not store secrets in its payload.

Modern Spring Security configures a SecurityFilterChain bean. For JWT APIs, prefer resource-server support over custom token parsing.

Use HTTPS, short token lifetimes, key rotation, issuer/audience validation, least privilege and safe refresh-token storage. Never trust client-supplied roles.

**Interview:** How does stateless logout work? An access token remains valid until expiry unless revocation/deny-list/key controls are added; refresh-token revocation is common.
