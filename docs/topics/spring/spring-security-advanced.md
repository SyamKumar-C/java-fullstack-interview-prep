# Spring Security: Filter Chain, Method Security and OAuth Resource Server

## 1. Short interview answer
Spring Security processes requests through a configured filter chain. Authentication establishes who the caller is; authorization decides what the caller may do. A resource server validates bearer access tokens and converts trusted claims/scopes into authorities. Method security adds checks at service method boundaries.

## 2. SecurityFilterChain
Define explicit matchers and a least-privilege default:

```java
@Bean
SecurityFilterChain apiSecurity(HttpSecurity http) throws Exception {
    return http
        .sessionManagement(s -> s.sessionCreationPolicy(
            SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/actuator/health").permitAll()
            .requestMatchers(HttpMethod.GET, "/api/orders/**")
                .hasAuthority("SCOPE_orders.read")
            .requestMatchers(HttpMethod.POST, "/api/orders/**")
                .hasAuthority("SCOPE_orders.write")
            .requestMatchers("/api/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated())
        .oauth2ResourceServer(oauth -> oauth.jwt(Customizer.withDefaults()))
        .build();
}
```

CSRF is enabled by default in common servlet security setups. Disable it only when the API's authentication design makes CSRF protection unnecessary—for example, a stateless API using bearer tokens in the Authorization header rather than browser cookies. Cookie/session authentication generally needs CSRF protection.

## 3. Request security flow
A simplified flow:
1. Request enters the servlet filter chain.
2. Security filters extract credentials and attempt authentication.
3. AuthenticationManager/AuthenticationProvider validates credentials.
4. A successful Authentication is stored in SecurityContext.
5. Authorization filters evaluate request rules.
6. Controller/service executes only if access is allowed.
7. Security context is cleared/managed at request completion.

Exact filter order and components depend on the configured authentication mechanisms.

## 4. Roles and authorities
- `hasRole("ADMIN")` commonly checks for authority `ROLE_ADMIN`.
- `hasAuthority("invoice.read")` checks the exact authority.
- OAuth JWT scopes are commonly mapped to `SCOPE_xxx` authorities by the resource-server converter.

Do not assume a role, scope and authority are interchangeable. Check the configured converter and naming convention.

## 5. Method security
```java
@Configuration
@EnableMethodSecurity
class MethodSecurityConfiguration {}

@Service
class InvoiceService {
    @PreAuthorize("hasAuthority('invoice.read')")
    public InvoiceDto find(long id) {
        return loadInvoice(id);
    }
}
```

Method security is useful when access rules belong close to business operations. Proxy-based method security has the same self-invocation limitation as other proxy features. Also verify ownership/tenant access: permission to call `invoice.read` does not automatically mean the caller may read every invoice ID.

## 6. JWT validation
A JWT is a signed token format, not proof of trust by itself. A resource server should validate:
- Signature using keys from a trusted issuer/JWK source.
- Issuer (`iss`).
- Expiry and not-before time where applicable.
- Audience (`aud`) for the intended API.
- Required scopes/claims and application-specific constraints.

Plan for key rotation and clock skew. Do not put secrets in token claims. Avoid trusting a token merely because it can be Base64-decoded.

## 7. OAuth 2.0 roles
- Authorization server: issues access tokens.
- Resource server: accepts and validates access tokens.
- Client: obtains tokens and calls protected resources.
- Access token: credential presented to an API.
- Refresh token: used with the authorization server to obtain new tokens; it is not normally sent to resource APIs.

OAuth 2.0 is an authorization framework. OpenID Connect adds an identity layer for user authentication.

## 8. CORS and CSRF
CORS controls whether browser JavaScript from one origin may access a response from another origin. It is not authentication and does not stop non-browser clients.

CSRF abuses credentials that browsers attach automatically, commonly cookies. Configure exact trusted origins, methods and headers. Do not combine wildcard origins with credentialed requests.

## 9. Multi-tenant security
Never trust a tenant ID from a request header merely because it is present. Derive or validate the tenant against the authenticated principal and enforce ownership at the service/data-access layer. Include tenant identity in cache keys and audit records where relevant.

## 10. Common mistakes
- Disabling CSRF for a cookie-authenticated application.
- Treating CORS as an authentication mechanism.
- Enforcing permissions only in Angular.
- Validating JWT signature but ignoring issuer/audience.
- Confusing `ROLE_ADMIN` and `SCOPE_admin`.
- Trusting client-supplied tenant IDs.
- Checking a general scope but not resource ownership.

## 11. Interview questions
**Q1. Authentication vs authorization?** Authentication verifies identity; authorization checks permissions.

**Q2. What is SecurityFilterChain?** A configured sequence of security filters applied to matching requests.

**Q3. What is a resource server?** An API that validates access tokens and authorizes requests based on trusted token information.

**Q4. Why use method security?** It protects business operations even when called from different controllers or entry points.

**Q5. Is CORS security/authentication?** It is a browser cross-origin access policy, not identity verification.

**Q6. Why validate audience?** To ensure a valid token was issued for this API, not merely for some other recipient.

## Practice
1. Protect read and write endpoints with different scopes.
2. Add a method-level rule and test denied access.
3. Explain CSRF settings for cookie sessions versus bearer-token APIs.
4. Validate tenant ownership before returning an order.
