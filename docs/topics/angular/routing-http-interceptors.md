# Angular Routing and HTTP Interceptors

## 1. Angular Router

The Router maps URL paths to components. It supports nested routes, route parameters, query parameters, lazy loading, guards and resolvers.

```ts
export const routes: Routes = [
  { path: 'users', loadComponent: () => import('./users/users.component').then(m => m.UsersComponent) },
  { path: 'users/:id', component: UserDetailsComponent },
  { path: '', redirectTo: 'users', pathMatch: 'full' },
  { path: '**', component: NotFoundComponent }
];
```

Use `router-outlet` as the location where the active route component is rendered.

## 2. Route parameters vs query parameters

- Route parameter: identifies a resource, e.g. `/users/42`.
- Query parameter: optional filter/state, e.g. `/users?page=2&status=active`.

```ts
const id = this.route.snapshot.paramMap.get('id');
this.route.queryParamMap.subscribe(params => {
  this.page = Number(params.get('page') ?? 1);
});
```

Use snapshots for one-time reads and subscriptions/signals for values that can change while the component remains active.

## 3. Guards

Common guards include:
- `CanActivate`: may a route be entered?
- `CanActivateChild`: may child routes be entered?
- `CanDeactivate`: may the user leave, e.g. unsaved form?
- `CanMatch`: should this route match/load?

A guard is a client-side navigation control, not a security boundary. The backend must still authorize every protected API request.

## 4. Resolvers and lazy loading

A resolver loads required route data before activation. Lazy loading loads a feature only when needed, reducing the initial bundle.

Use resolvers for data that must exist before the page is shown. Avoid putting every API call in a resolver because it can delay navigation.

## 5. HttpClient

`HttpClient` returns Observables. Requests are sent when subscribed to; subscribing twice to a cold HTTP Observable normally sends two requests.

```ts
@Injectable({ providedIn: 'root' })
export class UserService {
  private http = inject(HttpClient);
  getUser(id: number) {
    return this.http.get<User>(`/api/users/${id}`);
  }
}
```

## 6. HTTP interceptors

An interceptor can add common request behavior: authorization headers, logging, correlation IDs, error handling or loading indicators.

Functional interceptor example:

```ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(TokenService).getToken();
  const authReq = token
    ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } })
    : req;
  return next(authReq);
};
```

Register with `provideHttpClient(withInterceptors([authInterceptor]))`.

Requests are immutable, so clone them to change headers or parameters.

## 7. Error handling and retry

Handle expected API errors close to the service or in a shared interceptor, but preserve useful error details. Retry only operations that are safe to repeat and errors that may be temporary. Do not blindly retry non-idempotent POST requests.

## 8. Practical scenarios

- Protect an admin route with a guard, and enforce admin authorization on the server too.
- Add a bearer token centrally with an interceptor.
- Add a request ID header for tracing across frontend and backend.
- Use query parameters to keep table filters shareable in a URL.

## 9. Common mistakes

- Treating a route guard as backend security.
- Mutating a request instead of cloning it.
- Subscribing more than once and triggering duplicate HTTP calls.
- Retrying every error, including validation errors.
- Putting slow, non-essential data loading in a resolver.

## 10. Interview questions

**Q1. Guard vs interceptor?** A guard controls client-side navigation; an interceptor changes or observes HTTP requests/responses.

**Q2. Can a guard secure an API?** No. The backend must authenticate and authorize the API call.

**Q3. Why clone HttpRequest?** HttpRequest is immutable; clone creates a modified request.

**Q4. Why can an HTTP call execute twice?** HttpClient Observables are cold; each subscription can issue a new request.

**Q5. What is lazy loading?** Loading a route/feature only when it is needed.

## 11. Practice

Create a users route with an ID parameter, a query-parameter filter, an auth guard and an interceptor that adds a correlation ID.
