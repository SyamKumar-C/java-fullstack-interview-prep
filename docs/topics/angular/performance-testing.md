# Angular Performance and Testing

## 1. Where performance problems come from

Common causes include unnecessary change detection, large component trees, repeated calculations in templates, large bundles, too many HTTP calls, memory leaks, and rendering thousands of DOM elements.

Measure first. Use browser performance tools, Angular DevTools, bundle analysis and network timing instead of guessing.

## 2. Change-detection improvements

- Prefer `OnPush` where appropriate; it checks a component when relevant inputs/events/signals mark it for checking.
- Use signals or immutable state updates so changes are clear.
- Avoid calling expensive methods directly from templates.
- Track list items with a stable identity using `track` in modern control flow or `trackBy` with `*ngFor`.
- Use pure pipes for reusable transformations.

```ts
@Component({
  selector: 'app-users',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    @for (user of users(); track user.id) {
      <p>{{ user.name }}</p>
    }
  `
})
export class UsersComponent {
  users = signal<User[]>([]);
}
```

## 3. Rendering large lists

Use pagination or virtual scrolling when only a small part of a large list should be visible. Virtual scrolling reduces DOM nodes; it does not reduce the amount of data fetched unless paired with server-side loading.

## 4. Bundle and loading performance

- Lazy-load feature routes.
- Avoid importing large libraries for one small function.
- Use production builds and inspect bundle sizes.
- Use image optimization and appropriate caching.
- Defer non-critical UI where supported.
- Avoid duplicate API calls; share or cache data with a clear invalidation strategy.

## 5. Memory leaks

A component can be destroyed while a long-lived Observable subscription remains active. Prefer the async pipe for template streams or `takeUntilDestroyed()` for imperative subscriptions.

```ts
this.userService.users$
  .pipe(takeUntilDestroyed())
  .subscribe(users => this.users.set(users));
```

## 6. Testing levels

| Test | Scope | Example |
|---|---|---|
| Unit | One class/function | Service transformation |
| Component | Component + template | Button click updates view |
| Integration | Multiple parts together | Component calls mocked service |
| End-to-end | Full user flow | Login and create order |

## 7. Component test example

```ts
it('emits selected user when clicked', () => {
  const fixture = TestBed.createComponent(UserCardComponent);
  fixture.componentRef.setInput('userName', 'Asha');
  const emitted: string[] = [];
  fixture.componentInstance.selected.subscribe(v => emitted.push(v));

  fixture.detectChanges();
  const button = fixture.nativeElement.querySelector('button');
  button.click();

  expect(emitted).toEqual(['Asha']);
});
```

Use TestBed to configure dependencies and create the component. Set required inputs before change detection.

## 8. Service test example

```ts
it('loads users from the API', () => {
  const http = TestBed.inject(HttpTestingController);
  const service = TestBed.inject(UserService);

  service.getUsers().subscribe(users => expect(users.length).toBe(1));
  const req = http.expectOne('/api/users');
  expect(req.request.method).toBe('GET');
  req.flush([{ id: 1, name: 'Asha' }]);
  http.verify();
});
```

## 9. Practical scenarios

- A page with 10,000 rows: use server pagination or virtual scrolling.
- A slow route: inspect bundle size and lazy-load the feature.
- A component rerenders too often: inspect change-detection triggers and template calculations.
- A test is flaky: remove real network/time dependencies and control asynchronous work.

## 10. Common mistakes

- Optimizing without measuring.
- Assuming OnPush means a component never updates.
- Using virtual scroll when server-side pagination is also required.
- Testing private implementation details instead of visible behavior.
- Forgetting to verify that HTTP tests have no outstanding requests.

## 11. Interview questions

**Q1. How do you improve Angular performance?** Measure first, then reduce unnecessary checks, lazy-load routes, optimize bundles, avoid repeated work and render large lists efficiently.

**Q2. What does OnPush do?** It narrows when Angular checks a component; events, changed inputs, signals and explicit marking can still schedule checks.

**Q3. Unit vs E2E test?** Unit tests isolate a small unit; E2E tests exercise a complete user flow through the application.

**Q4. How do you test HttpClient?** Use HttpTestingController to inspect requests and provide controlled responses.

## 12. Practice

Profile a list page, add stable tracking, move expensive calculations out of the template, and write a component test plus an HTTP service test.
