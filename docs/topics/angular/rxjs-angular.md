# RxJS in Angular

## 1. Why Angular uses RxJS

RxJS represents values that arrive over time as Observables. Angular uses it in HttpClient, router events, reactive forms and many event-driven workflows.

An Observable does not usually start work until it is subscribed to. It can emit zero or more values, complete, or error.

## 2. Observable and subscription

```ts
const numbers$ = new Observable<number>(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

const subscription = numbers$.subscribe({
  next: value => console.log(value),
  error: error => console.error(error),
  complete: () => console.log('done')
});
```

A Subscription represents an active execution and can be unsubscribed.

## 3. Operators

| Operator | Purpose | Typical use |
|---|---|---|
| `map` | Transform each value | Convert API response |
| `filter` | Keep matching values | Ignore empty input |
| `tap` | Side effect without changing value | Logging |
| `switchMap` | Switch to latest inner Observable | Search autocomplete |
| `concatMap` | Run inner Observables in order | Ordered saves |
| `mergeMap` | Run inner Observables concurrently | Independent uploads |
| `catchError` | Recover from an error | Show fallback |
| `debounceTime` | Wait for a quiet period | Search input |
| `distinctUntilChanged` | Ignore consecutive duplicates | Avoid repeat calls |

## 4. Search example

```ts
this.searchControl.valueChanges.pipe(
  debounceTime(300),
  map(value => value?.trim() ?? ''),
  distinctUntilChanged(),
  switchMap(query => query
    ? this.userService.search(query)
    : of([])
  ),
  takeUntilDestroyed()
).subscribe(users => this.users.set(users));
```

`switchMap` is useful because a newer search cancels the previous subscription. It prevents stale results from winning, although cancellation of the client subscription does not guarantee the server stopped processing.

## 5. Async pipe vs manual subscribe

Prefer `async` pipe when the template consumes a stream. It subscribes and unsubscribes with the view.

```html
@if (users$ | async; as users) {
  @for (user of users; track user.id) {
    <p>{{ user.name }}</p>
  }
}
```

For imperative work, use `takeUntilDestroyed()` or another explicit cleanup strategy.

## 6. Subjects

- `Subject`: multicast values to current subscribers; no stored current value.
- `BehaviorSubject`: requires an initial value and emits the latest value to new subscribers.
- `ReplaySubject`: replays a configured number/time window of previous values.
- `AsyncSubject`: emits the last value only when it completes.

Do not use a Subject as a default state-management tool when a signal or a simple service property is enough.

## 7. Error handling

An Observable error terminates that execution. Put `catchError` at the correct level. If it is inside `switchMap`, one request can fail while future search terms still work. If it is outside, the whole outer stream may complete after the error depending on the replacement Observable.

## 8. Practical scenarios

- Type-ahead search: debounce + distinct + switchMap.
- Ordered form saves: concatMap.
- Independent file uploads: mergeMap with a concurrency limit.
- Component state: BehaviorSubject or signals depending on the need.
- Template display: async pipe.

## 9. Common mistakes

- Forgetting to unsubscribe from long-lived streams.
- Using `mergeMap` for search and showing stale responses.
- Calling subscribe inside subscribe instead of composing operators.
- Assuming `catchError` always keeps the original stream alive.
- Sharing a stream without understanding replay and ref-count behavior.

## 10. Interview questions

**Q1. Why does Angular use Observables?** They model asynchronous streams and provide composition, cancellation and operators.

**Q2. Why use switchMap for search?** It switches to the latest request subscription and prevents older results from updating the UI.

**Q3. Async pipe vs subscribe?** Async pipe manages the template subscription; manual subscribe is for imperative side effects and needs lifecycle cleanup.

**Q4. Subject vs BehaviorSubject?** BehaviorSubject stores a current value and emits it immediately to a new subscriber.

## 11. Practice

Build a search box using FormControl, debounceTime, distinctUntilChanged, switchMap and catchError. Verify that typing quickly does not display older search results.
