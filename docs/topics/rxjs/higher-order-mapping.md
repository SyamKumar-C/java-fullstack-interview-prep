# Higher-Order Observables and Mapping Operators

A higher-order Observable emits Observables. Flattening operators subscribe to inner streams.

- switchMap: cancel previous inner stream when a new value arrives; useful for latest search.
- mergeMap: run inner streams concurrently; limit concurrency when needed.
- concatMap: queue inner streams in order.
- exhaustMap: ignore new triggers while current inner work is active; useful for submit buttons.

```ts
searchTerms$.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => http.get('/api/search', { params: { q: term } }))
);
```

**Interview:** Which operator prevents repeated submit clicks while a request is active? exhaustMap is often suitable.
