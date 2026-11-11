# Spring Security: Filter Chain, Method Security and OAuth Resource Server

> Priority: P1 | Level: Intermediate | Study time: 55 minutes

## 1. Short interview answer
Spring Security applies a chain of servlet filters to incoming requests. Authentication establishes the principal; authorization checks access. A resource server validates bearer access tokens and maps trusted claims/scopes to authorities. Method security adds checks close to business operations.

## 2. Security filter chain
A SecurityFilterChain defines which requests are matched and which security rules apply. Prefer explicit request matchers and least privilege.

```java
@Bean
SecurityFilterChain apiSecurity(HttpSecurity http) throws Exception {
    return http
        .csrf(csrf -> csrf.disable()) // only appropriate for a stateless bearer-token API
        .sessionManagement(session -> session
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/actuator/health").permitAll()
            .requestMatchers(HttpMethod.GET, "/api/orders/**")
                .hasAuthority("SCOPE_orders.read")
            .requestMatchers("/api/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated())
        .oauth2ResourceServer(oauth -> oauth.jwt(Customizer.withDefaults()))
        .build();
}
```

Do not disable CSRF blindly. It is commonly disabled for stateless APIs that accept bearer tokens in the Authorization header and do not rely on ambient browser credentials. Cookie/session authentication needs CSRF protection.

## 3. Authentication and authorization
Authentication creates an authenticated principal. Authorization checks whether the principal has required roles/authorities. Roles are commonly represented with a ROLE_ prefix; OAuth scopes are often represented as SCOPE_ authorities. Know the exact mapping configured by the application.

## 4. Method security

```java
@Configuration
@EnableMethodSecurity
class MethodSecurityConfig {}

@Service
class InvoiceService {
    @PreAuthorize("hasAuthority('invoice.read')")
    public InvoiceDto getInvoice(long id) {
        return loadInvoice(id);
    }
}
```

Method security protects calls through Spring's proxy. Self-invocation and non-Spring-managed objects can bypass proxy-based checks. Do not use method security as a substitute for validating object ownership and tenant boundaries.

## 5. JWT resource server
Validate signature using trusted issuer keys, issuer, audience, expiry, and required claims. Configure issuer/audience validation. A decoded JWT is not necessarily a valid JWT. Plan for key rotation and token expiry. Do not put secrets or unnecessary personal data in token claims.

## 6. CORS and CSRF
CORS is a browser policy controlling cross-origin script access; it is not authentication. CSRF exploits automatically attached credentials such as cookies. Configure exact trusted origins, methods, and headers; avoid wildcard origins with credentials.

## 7. Common mistakes
- Disabling CSRF for a cookie-authenticated application.
- Treating CORS as an API security control.
- Checking a role in Angular but not in backend.
- Validating JWT signature but ignoring issuer/audience.
- Confusing ROLE_ADMIN and SCOPE_admin.
- Trusting tenant ID supplied by client without checking principal permissions.

## 8. Interview questions
**Q1. What is SecurityFilterChain?** A configured chain of security filters applied to matching requests.

**Q2. Authentication vs authorization?** Identity verification vs permission checking.

**Q3. Why is CSRF often disabled for stateless bearer APIs?** The browser does not automatically attach a bearer token stored outside cookies in the same way it attaches cookies; this assumption must match the actual token storage/design.

**Q4. What is method security?** Authorization checks on method invocation, commonly using annotations such as PreAuthorize.

**Q5. Is CORS authentication?** No. It is a browser cross-origin access policy.

## 9. Practice
1. Protect GET and POST order endpoints with different scopes.
2. Add a method-level authorization rule.
3. Explain CSRF choices for cookie sessions versus bearer-token APIs.
4. Validate tenant ownership in a service method.
