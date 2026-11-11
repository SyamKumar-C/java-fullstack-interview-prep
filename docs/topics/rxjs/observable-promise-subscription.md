# RxJS Observable, Promise and Subscription

An Observable emits zero or more values over time, then completes or errors. It is lazy by default; subscription starts work.

| Promise | Observable |
|---|---|
| One eventual result | Zero or many values |
| Starts when created | Usually starts on subscribe |
| Not cancellable by default | Subscription can be cancelled |
| Built-in chaining | Rich operator composition |

```ts
const subscription = source$.subscribe({
  next: value => console.log(value),
  error: error => console.error(error),
  complete: () => console.log('done')
});
subscription.unsubscribe();
```

Completion/error ends the subscription automatically. HttpClient Observables usually emit once and complete; long-lived streams need lifecycle management.

**Interview:** Does unsubscribe cancel underlying work? It cancels the subscription and may cancel the producer if teardown supports it.
