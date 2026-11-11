# Spring Security and JWT Authentication

## 1. Authentication and authorization

- Authentication answers: who is calling?
- Authorization answers: what is this caller allowed to do?

A typical API authenticates a user, creates an authenticated security context, and checks roles/authorities before allowing protected operations.

## 2. Security filter chain

Spring Security processes HTTP requests through a configured filter chain. Filters can extract credentials, authenticate them, establish the SecurityContext, and apply authorization rules before the controller runs.

Modern Spring Security configuration uses a SecurityFilterChain bean rather than extending the old WebSecurityConfigurerAdapter pattern.

```java
@Bean
SecurityFilterChain security(HttpSecurity http) throws Exception {
    return http
        .csrf(csrf -> csrf.disable()) // only appropriate for a stateless API with no cookie-based browser auth
        .sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/auth/**").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated())
        .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))
        .build();
}
```

Disabling CSRF is not a universal best practice. It may be appropriate for a bearer-token API that does not rely on automatically attached browser cookies. Cookie-based authentication needs CSRF protection.

## 3. JWT structure

A signed JWT commonly has three Base64URL-encoded parts:
1. Header: token type and signing algorithm.
2. Payload: claims such as subject, issuer, audience, expiry, and roles.
3. Signature: verifies integrity and issuer authenticity.

A signed JWT is not encrypted by default. Do not put passwords, secrets, or sensitive personal data in its payload.

## 4. Token validation

A resource server should validate:
- Signature against a trusted key.
- Allowed signing algorithm.
- Issuer.
- Audience where applicable.
- Expiry and not-before timestamps.
- Required claims and authority mapping.

Never trust a token just because it can be Base64-decoded. Avoid accepting an algorithm chosen blindly from the token header.

## 5. Access and refresh tokens

Access tokens are short-lived credentials sent to APIs. Refresh tokens are longer-lived credentials used to obtain new access tokens and need stronger protection, rotation, and revocation strategy.

A logout or password change does not automatically invalidate a self-contained JWT unless the system has a revocation or versioning mechanism. Short expiry, refresh-token rotation, and server-side session/token state can help.

## 6. Roles and authorities

Spring Security authorities are permission strings. `hasRole("ADMIN")` commonly checks for `ROLE_ADMIN`; `hasAuthority("ORDER_READ")` checks an exact authority. Be consistent in claim-to-authority conversion.

## 7. Common mistakes

- Treating JWT as encrypted data.
- Skipping issuer/audience/expiry validation.
- Storing long-lived tokens in unsafe browser storage without considering XSS.
- Disabling CSRF while using cookie-based authentication.
- Logging bearer tokens.
- Assuming logout automatically revokes a stateless token.
- Confusing roles with fine-grained permissions.

## 8. Interview questions and answers

**Q1. What is JWT?**  
A compact token format often used to carry signed claims between parties. A signed JWT provides integrity, not confidentiality.

**Q2. Authentication versus authorization?**  
Authentication verifies identity; authorization checks permissions.

**Q3. What does stateless mean in a JWT API?**  
The server does not need a traditional HTTP session for each request; it validates the bearer token on each request. It may still keep revocation or refresh state.

**Q4. What should be validated in a JWT?**  
Signature, trusted algorithm, issuer, audience where required, expiry, not-before, and required claims.

**Q5. Why use short-lived access tokens?**  
They reduce the time window in which a stolen token can be used. Refresh-token controls are still required.

**Q6. Should CSRF be disabled?**  
Only when the authentication design does not rely on credentials automatically attached by the browser, such as a carefully designed bearer-token API. Cookie-based auth needs CSRF protection.

## 9. Practice

Configure a Spring Boot resource server that validates JWTs from a trusted issuer. Add an ADMIN-only endpoint and explain how role claims become Spring authorities.

## 10. Quick revision

JWT is signed, not encrypted by default. Validate signature and claims. Authentication identifies; authorization permits. CSRF depends on how credentials are sent.
