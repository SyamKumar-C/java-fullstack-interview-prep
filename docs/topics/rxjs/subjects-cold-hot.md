# RxJS Subjects, Cold and Hot Observables

## 1. Cold Observable

A cold Observable starts its producer separately for each subscriber. Each subscriber gets its own execution.

```ts
const source$ = new Observable<number>(subscriber => {
  console.log('producer started');
  subscriber.next(Math.random());
  subscriber.complete();
});

source$.subscribe(console.log);
source$.subscribe(console.log); // producer runs again
```

Angular HttpClient Observables are cold: two subscriptions normally send two HTTP requests.

## 2. Hot Observable

A hot Observable is backed by a producer that can emit independently of a particular subscriber. Subscribers receive values emitted while they are subscribed; earlier values may be missed unless the source replays them.

DOM events and shared subjects are common examples.

## 3. Subject

A Subject is both an Observable and an Observer. It multicasts values to current subscribers.

```ts
const subject = new Subject<number>();
subject.subscribe(v => console.log('A', v));
subject.next(1);
subject.subscribe(v => console.log('B', v));
subject.next(2);
```

A receives 1 and 2; B receives only 2.

## 4. Subject types

| Type | Behavior |
|---|---|
| Subject | No stored current value; emits to active subscribers |
| BehaviorSubject | Requires initial value; new subscriber gets current value |
| ReplaySubject | Replays configured prior values/time window |
| AsyncSubject | Emits last value only when source completes |

BehaviorSubject is useful for simple current-state streams, but modern Angular signals may be simpler for local synchronous state.

## 5. Sharing an Observable

`share()` shares a source among overlapping subscribers. `shareReplay()` also replays buffered values to later subscribers.

```ts
readonly users$ = this.http.get<User[]>('/api/users').pipe(
  shareReplay({ bufferSize: 1, refCount: true })
);
```

Understand refCount, source completion and cache invalidation. shareReplay is not a universal permanent cache.

## 6. Cold vs hot comparison

- Cold: subscription starts an independent execution.
- Hot: producer exists independently and subscribers join the ongoing stream.
- A cold source can be made shared/hot-like using multicasting operators.
- A Subject is a common hot source.

## 7. Practical scenarios

- HTTP request shared by several components: shareReplay with a deliberate lifetime.
- Current logged-in user: BehaviorSubject or signal in an auth/state service.
- Broadcast a notification event: Subject.
- Replay last few application events: ReplaySubject with a bounded buffer.

## 8. Common mistakes

- Assuming a Subject replays values to new subscribers.
- Using ReplaySubject without a buffer limit and retaining too much data.
- Using shareReplay without understanding stale cached values.
- Exposing a writable Subject publicly; expose asObservable() to protect writes.
- Forgetting that subscribing twice to a cold HTTP Observable can send two requests.

## 9. Interview questions

**Q1. Cold vs hot?** Cold starts a producer per subscription; hot shares a producer independent of subscribers.

**Q2. Subject vs BehaviorSubject?** BehaviorSubject has a current value and emits it immediately to new subscribers.

**Q3. ReplaySubject vs BehaviorSubject?** ReplaySubject can replay a configured history; BehaviorSubject stores one current value and requires an initial value.

**Q4. Why shareReplay?** To share one source execution and replay selected values to subscribers.

**Q5. Can two subscriptions cause two HTTP requests?** Yes, unless the source is shared/cached appropriately.

## 10. Practice

Create one cold Observable and subscribe twice. Then share it. Compare Subject, BehaviorSubject and ReplaySubject by subscribing before and after emissions.
