# Angular Routing, State and Performance

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Routing
Angular Router maps URL paths to components. Routes can include parameters, query parameters, guards, resolvers, lazy loading, and providers.

```ts
export const routes: Routes = [
  {
    path: 'orders/:id',
    loadComponent: () => import('./order-detail.component')
      .then(m => m.OrderDetailComponent),
    canActivate: [authGuard]
  },
  { path: '', redirectTo: 'orders', pathMatch: 'full' }
];
```

Read a route parameter:

```ts
const route = inject(ActivatedRoute);
const orderId = route.snapshot.paramMap.get('id');
```

Use observable route parameters when the same component instance can stay active while the parameter changes.

## 2. Guards and resolvers
Guards decide whether navigation may proceed. Resolvers load data before route activation. A guard is not backend security: users can call APIs directly, so every backend request must enforce authorization.

```ts
export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  const router = inject(Router);
  return auth.isLoggedIn() || router.createUrlTree(['/login']);
};
```

## 3. Lazy loading
Lazy loading downloads a feature only when needed. It reduces initial bundle size but can add delay on first navigation. Preloading can help when a user is likely to visit a feature soon.

## 4. State management
- Component state: local UI state.
- Service with signals/RxJS: shared state within a feature or application.
- Store library: complex state with many events, effects, selectors, and debugging needs.
- URL state: filters, sorting, and pagination that should survive refresh or be shareable.

Keep state as close as practical to the components that use it. A global store is not required for every small application.

## 5. Performance checklist
- Lazy-load feature routes.
- Use stable tracking keys in repeated lists.
- Prefer immutable updates where appropriate with OnPush.
- Avoid expensive function calls in templates.
- Use pure pipes for display transformations.
- Debounce search input and cancel stale requests with switchMap.
- Use async pipe or takeUntilDestroyed for subscription lifecycle.
- Inspect bundle size and browser performance rather than guessing.

## 6. Common mistakes
- Treating route guards as backend authorization.
- Reading a snapshot when route parameters can change.
- Lazy-loading every tiny component.
- Keeping shareable filters only in memory.
- Using array index as tracking key for reorderable lists.

## 7. Interview questions
**Q1. What is lazy loading?** Loading a feature bundle only when it is needed.

**Q2. Guard vs resolver?** Guard controls navigation; resolver fetches data before activation.

**Q3. Are guards secure?** No. Backend APIs must enforce authorization.

**Q4. When should state go in the URL?** When it should survive refresh or be shareable, such as filters and pagination.

**Q5. How do you prevent stale search responses?** Debounce input and use switchMap so newer searches replace older subscriptions.

## 8. Practice
1. Build a lazy-loaded order detail route.
2. Add a guard and explain why the API still needs authorization.
3. Store filters in query parameters.
4. Profile a large list and compare tracking strategies.
