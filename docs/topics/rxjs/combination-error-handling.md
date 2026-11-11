# RxJS Combination and Error Handling Operators

## 1. Why combine Observables?

A UI often needs data from multiple asynchronous sources: user profile, permissions, dashboard metrics or route parameters. RxJS provides operators that define how streams combine and how failures are handled.

## 2. combineLatest

Emits whenever any input emits, but only after every input has emitted at least once. The output contains the latest value from each source.

```ts
combineLatest([filters$, page$]).pipe(
  switchMap(([filters, page]) => api.search(filters, page))
);
```

Use it when the result depends on the latest state of several streams.

## 3. forkJoin

Waits for all input Observables to complete, then emits their final values once. If one input never completes, forkJoin never emits.

```ts
forkJoin({
  profile: api.getProfile(),
  roles: api.getRoles(),
  settings: api.getSettings()
}).subscribe(result => render(result));
```

This is useful for finite HTTP requests that must all finish before rendering.

## 4. zip and withLatestFrom

- zip pairs the first values together, then second values, and so on.
- withLatestFrom emits when the source Observable emits, combining it with the latest values from other streams.

Use withLatestFrom when a button click should use the latest form state; use zip when emissions should be paired by position.

## 5. merge and concat

- merge combines emissions as they arrive, allowing interleaving.
- concat subscribes to each source in sequence, waiting for one to complete before the next.

## 6. Error handling

An Observable error terminates that execution. `catchError` replaces the failed stream with another Observable.

```ts
api.getUsers().pipe(
  catchError(error => {
    logError(error);
    return of([]);
  })
);
```

The fallback value should match the expected type and business behavior.

## 7. catchError placement

Inside a higher-order operator, the failed inner request can be replaced while the outer stream continues:

```ts
searchTerms$.pipe(
  switchMap(term => api.search(term).pipe(
    catchError(() => of([]))
  ))
);
```

If catchError is outside switchMap, an error may terminate the whole search stream after the replacement completes. Placement changes behavior.

## 8. retry and finalize

- retry resubscribes after an error. Use bounded retry and only when repeating the operation is safe.
- retryWhen (legacy pattern) or newer retry configuration can control retry behavior.
- finalize runs on completion, error or unsubscription; useful for clearing loading state.

Do not retry validation failures or unsafe non-idempotent requests blindly.

## 9. Practical scenarios

- Initial dashboard: forkJoin for several finite HTTP calls.
- Live filters: combineLatest for latest filter and page.
- Submit click with latest form value: withLatestFrom.
- Search that should survive one failed request: catchError inside switchMap.
- Loading indicator: finalize.

## 10. Common mistakes

- Using forkJoin with a stream that never completes.
- Expecting combineLatest to emit before each source has emitted.
- Using zip when latest-value behavior is needed.
- Placing catchError outside a long-lived stream and accidentally ending it.
- Retrying every error without limit.
- Forgetting finalize also runs on unsubscription.

## 11. Interview questions

**Q1. combineLatest vs forkJoin?** combineLatest emits whenever inputs change after all have emitted; forkJoin emits once after all complete.

**Q2. merge vs concat?** merge interleaves emissions; concat subscribes sequentially.

**Q3. zip vs combineLatest?** zip pairs emissions by index; combineLatest uses the latest value from each source.

**Q4. What does catchError do?** Replaces an errored Observable with another Observable.

**Q5. What does finalize do?** Runs on completion, error or unsubscription.

## 12. Practice

Load profile, roles and settings with forkJoin. Then build a search flow using combineLatest, switchMap and inner catchError. Add a loading indicator with finalize.
