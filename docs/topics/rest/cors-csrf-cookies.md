# CORS, CSRF and Cookies

## 1. Same-origin policy

Browsers restrict scripts from reading responses from a different origin. An origin is the combination of scheme, host, and port. For example, http://localhost:4200 and http://localhost:8080 are different origins because their ports differ.

CORS is a browser-enforced mechanism that lets a server state which cross-origin requests may be shared with browser JavaScript.

## 2. CORS request flow

For a simple request, the browser sends the request and checks the response's CORS headers before exposing the response to JavaScript.

For a non-simple request, the browser may first send an OPTIONS preflight asking whether the method and headers are allowed. The server responds with allowed origin, methods, and headers.

Common response headers:
- Access-Control-Allow-Origin
- Access-Control-Allow-Methods
- Access-Control-Allow-Headers
- Access-Control-Allow-Credentials
- Access-Control-Max-Age

## 3. Credentials and wildcard

When credentials such as cookies are included, the server must return a specific allowed origin, not `*`, and must explicitly allow credentials. The browser also requires the client request to opt into credentials.

CORS is not authentication. It controls whether browser JavaScript can read a cross-origin response; it does not stop curl, mobile apps, or server-to-server clients.

## 4. CSRF

Cross-Site Request Forgery tricks a browser into sending an authenticated request to a site where the user is already logged in. It is especially relevant when browsers automatically attach credentials such as session cookies.

Defenses include CSRF tokens, SameSite cookie settings, origin/referrer checks as additional controls, and avoiding state-changing GET endpoints.

## 5. Cookies

Important cookie attributes:
- HttpOnly: JavaScript cannot read the cookie.
- Secure: send only over HTTPS.
- SameSite=Strict/Lax/None: controls cross-site sending behavior; None requires Secure in modern browsers.
- Domain and Path: scope where the cookie is sent.
- Max-Age/Expires: lifetime.

HttpOnly reduces token theft through JavaScript access but does not stop an XSS attacker from performing actions as the user from the page.

## 6. CORS versus CSRF versus XSS

- CORS: browser policy for cross-origin response access.
- CSRF: attacker causes a victim's browser to send an authenticated request.
- XSS: attacker runs malicious script in a trusted origin.

They are different risks and need different controls.

## 7. Spring Boot scenario

Configure allowed origins, methods, and headers deliberately. Ensure CORS processing occurs in the right place in the Spring Security filter chain so preflight OPTIONS requests are handled before authentication blocks them. Do not use broad wildcard origins with credentialed requests.

## 8. Interview questions and answers

**Q1. What is CORS?**  
A browser mechanism where a server declares which cross-origin requests may expose responses to browser JavaScript.

**Q2. What is a preflight request?**  
An OPTIONS request the browser sends before certain cross-origin requests to check allowed methods and headers.

**Q3. Is CORS a security mechanism against curl?**  
No. CORS is enforced by browsers. Non-browser clients are not restricted by browser same-origin policy.

**Q4. Why can't Access-Control-Allow-Origin be * with credentials?**  
Credentialed cross-origin access requires the server to name the allowed origin explicitly so the browser can enforce the policy.

**Q5. Why is CSRF relevant to cookies?**  
Browsers automatically attach matching cookies, so a malicious site may cause a request to carry the user's session unless defenses are applied.

**Q6. Does HttpOnly prevent XSS?**  
No. It prevents JavaScript from reading the cookie, but injected script may still perform actions as the user.

## 9. Practice

An Angular app at localhost:4200 calls Spring Boot at localhost:8080 using a session cookie. Explain the preflight, credential settings, explicit allowed origin, and CSRF token requirement.

## 10. Quick revision

CORS controls browser response access. CSRF abuses automatically attached credentials. Cookies need Secure, HttpOnly, and suitable SameSite settings.
