# Observable, Promise and Subscription in RxJS

## 1. Why asynchronous programming matters

An asynchronous operation finishes later: an HTTP request, user input, timer, WebSocket message, or file operation. JavaScript should not block while waiting. Promises and RxJS Observables represent values that arrive in the future, but they have different behavior.

## 2. Promise

A Promise represents one eventual result: fulfilled or rejected.

```ts
const result = fetch('/api/users')
  .then(response => {
    if (!response.ok) throw new Error('Request failed');
    return response.json();
  })
  .then(users => console.log(users))
  .catch(error => console.error(error));
```

Key points:
- A Promise starts its work when it is created.
- It settles once: fulfilled or rejected.
- A settled Promise cannot be reset.
- `then`, `catch`, and `finally` create/return Promise chains.
- A Promise has no built-in cancellation protocol. AbortController can cancel supported work such as fetch.

## 3. Observable

An Observable represents a stream of notifications over time. It may emit zero, one, or many values, then complete or error.

```ts
import { Observable } from 'rxjs';

const seconds$ = new Observable<number>(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

seconds$.subscribe({
  next: value => console.log(value),
  error: error => console.error(error),
  complete: () => console.log('complete')
});
```

An Observable is usually lazy: creating it describes the work; subscribing starts execution. Each subscription can create a separate execution unless the source is shared or multicast.

## 4. Observer and subscription

An Observer provides notification handlers:
- `next(value)`: a value arrived.
- `error(error)`: the stream failed; no more notifications follow.
- `complete()`: the stream finished normally; no more notifications follow.

`subscribe()` returns a Subscription. Calling `unsubscribe()` stops that subscription and runs teardown logic.

```ts
const sub = interval(1000).subscribe(value => console.log(value));
sub.unsubscribe();
```

Unsubscribe stops delivery to that observer. It cancels underlying work only if the producer supports cancellation and implements suitable teardown. For example, RxJS timer/interval teardown clears its timer.

## 5. Promise versus Observable

| Feature | Promise | Observable |
|---|---|---|
| Values | One eventual value or error | Zero or many values |
| Start | Work starts when Promise is created | Usually starts on subscribe |
| Cancellation | No built-in cancellation; APIs may support AbortController | Unsubscribe; producer teardown determines actual cancellation |
| Operators | then/catch/finally | map, filter, switchMap, retry, etc. |
| Multiple consumers | Same settled result | Each subscription may run source again |
| Completion | Settles once | Explicit complete notification |
| Typical use | One result from an async operation | Events, changing values, composed async streams |

## 6. Converting between them

```ts
import { from, firstValueFrom, lastValueFrom } from 'rxjs';

const promise = fetch('/api/users');
const response$ = from(promise); // emits the Promise result once

const user = await firstValueFrom(user$); // first emitted value
const finalValue = await lastValueFrom(finite$); // final value before completion
```

Do not use `lastValueFrom` on a stream that never completes; its Promise will remain pending. Use a finite stream or operators such as `take(1)` when that matches the requirement.

## 7. Angular HttpClient scenario

Angular HttpClient methods return Observables. The request generally starts on subscription. Unsubscribing from an in-flight HttpClient request aborts the client-side request, but it cannot guarantee that a server which already received it stops processing.

```ts
this.http.get<User[]>('/api/users')
  .subscribe({
    next: users => this.users = users,
    error: err => this.error = 'Could not load users'
  });
```

In a component, prefer the async pipe or lifecycle-aware patterns such as `takeUntilDestroyed` to avoid subscriptions outliving the component.

## 8. Cold and hot behavior preview

A cold Observable starts a separate producer for each subscriber (for example, a basic `of` or `HttpClient.get`). A hot source emits independently of a particular subscriber (for example, a shared event stream). Sharing operators can change subscription behavior; see the Subjects and cold/hot lesson.

## 9. Common mistakes

- Thinking an Observable runs just because it was created.
- Forgetting to subscribe or use the async pipe.
- Assuming unsubscribe always cancels server-side work.
- Calling `lastValueFrom` on an infinite stream.
- Leaving long-lived subscriptions active after a component is destroyed.
- Treating an Observable as automatically faster or better than a Promise.

## 10. Interview questions and answers

**Q1. What is the main difference between Promise and Observable?**  
A Promise represents one eventual result. An Observable represents a stream of zero or more values over time and provides operators and subscription teardown.

**Q2. Is an Observable lazy?**  
Usually, yes. A typical cold Observable starts when subscribed. Some Observables wrap already-running or hot sources, so laziness is not a universal guarantee for every source.

**Q3. What does subscribe return?**  
A Subscription. It can be unsubscribed to stop that observer and invoke producer teardown.

**Q4. Does unsubscribe cancel an HTTP request?**  
Angular HttpClient supports cancellation of the client-side in-flight request on unsubscribe. It does not guarantee that server processing is rolled back or stopped.

**Q5. Can an Observable emit multiple values?**  
Yes. For example, a WebSocket or interval can emit repeatedly until it errors, completes, or is unsubscribed.

**Q6. Why use an Observable for a button click?**  
Events naturally form a stream. Operators can debounce, filter, combine, and cancel work based on later clicks.

**Q7. When would you choose a Promise?**  
For a single result where Promise composition is sufficient, such as a one-time async operation. Use Observable when the stream or operator composition is useful.

## 11. Practice exercises

1. Create an Observable that emits 10, 20, 30 and completes.
2. Create an interval and unsubscribe after three values.
3. Convert a Promise to an Observable using `from`.
4. Convert a finite Observable to a Promise using `firstValueFrom`.
5. Explain what happens if `lastValueFrom` receives an interval that never completes.

## 12. Quick revision

Promise: one result, eager start, no built-in cancellation. Observable: stream, usually lazy, operator composition, subscription teardown. Subscription controls observation; producer teardown controls actual cancellation.
