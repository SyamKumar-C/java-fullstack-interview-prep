# RxJS Combination and Error Handling

combineLatest emits when any input emits, after each has emitted once. forkJoin waits for all inputs to complete, then emits their last values (useful for finite HTTP calls). zip pairs values by index. merge forwards values as they arrive. concat subscribes one source after another.

catchError replaces a failed stream. retry repeats subscription; use carefully for non-idempotent side effects.

```ts
http.get<Data>('/api/data').pipe(
  retry({ count: 2, delay: 500 }),
  catchError(error => of({ fallback: true } as Data))
);
```

Place catchError inside switchMap when one request failure should not terminate the outer search stream. Know tap, finalize, timeout, shareReplay, takeUntil and takeUntilDestroyed.

**Interview:** forkJoin vs combineLatest? Wait for all to complete and emit once vs emit whenever an input changes after all have emitted.
