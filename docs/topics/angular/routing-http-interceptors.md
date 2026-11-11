# Angular Routing, HttpClient and Interceptors

Routes map URLs to components. Route parameters identify resources; query parameters represent optional filters. Guards improve navigation experience but are not the security boundary; backend authorization is required.

Lazy loading reduces initial bundle size. HttpClient returns cold Observables: request starts on subscription, and multiple subscriptions can send multiple requests.

Interceptors can add auth headers/correlation IDs, handle common errors and measure requests. Avoid retrying non-idempotent requests without idempotency protection.

**Interview:** Does HttpClient.get() send immediately? No; it returns a cold Observable and the request runs on subscription.
