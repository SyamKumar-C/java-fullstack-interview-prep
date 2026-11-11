# CORS, CSRF and Cookies

## CORS
Cross-Origin Resource Sharing is a browser security mechanism. The server tells the browser which origins, methods and headers are allowed. It is not authentication and does not protect an API from curl/Postman clients.

Some cross-origin requests trigger an OPTIONS preflight.

When credentials are allowed, use a specific allowed origin, not wildcard origin, and configure credentials explicitly.

## CSRF
Cross-Site Request Forgery abuses automatic browser credentials, often cookies. Defenses include CSRF tokens, SameSite cookies and origin checks. Bearer tokens not automatically attached as cookies have a different CSRF profile, but XSS/token theft remain risks.

## Cookie flags
HttpOnly prevents JavaScript reading the cookie. Secure sends only over HTTPS. SameSite controls cross-site sending. Path/Domain scope the cookie.

**Interview:** Does CORS stop curl? No. Does HttpOnly prevent XSS? No; it only limits script access to the cookie.
