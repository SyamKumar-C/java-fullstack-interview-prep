# RxJS Higher-Order Mapping: switchMap, mergeMap, concatMap and exhaustMap

## 1. What is a higher-order Observable?

A higher-order Observable emits values that are themselves Observables. This often happens when an event triggers an HTTP request.

```ts
const clicks$ = fromEvent(button, 'click');
const requests$ = clicks$.pipe(map(() => http.get('/api/data')));
```

The result is a stream of Observables, not yet a flattened stream of response values. Higher-order mapping operators subscribe to the inner streams and combine their outputs.

## 2. The four important operators

### switchMap
When a new outer value arrives, unsubscribe from the previous inner Observable and subscribe to the latest one.

Use for search-as-you-type: old search results are no longer relevant.

```ts
searchTerms$.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => this.http.get('/api/search', { params: { term } }))
);
```

Unsubscription may cancel an Angular HttpClient request in flight, but cannot guarantee server-side work stops after the request reached the server.

### mergeMap
Subscribe to inner Observables concurrently. Results can arrive in completion order, not necessarily source order.

Use when independent operations can run at the same time, such as loading details for multiple IDs. Supply a concurrency limit when needed.

### concatMap
Queue inner Observables and run them one at a time in source order.

Use when order matters, such as saving a sequence of user changes. A slow inner stream can cause a growing queue if outer values arrive faster than they are processed.

### exhaustMap
If an inner Observable is active, ignore new outer values until it completes.

Use for preventing repeated submit clicks while one login/payment request is running. It ignores new triggers; it does not queue them.

## 3. Comparison

| Operator | New outer value while inner active | Order |
|---|---|---|
| switchMap | Cancels previous inner subscription | Latest result matters |
| mergeMap | Starts another inner stream | Completion order |
| concatMap | Queues it | Source order |
| exhaustMap | Ignores it | First active operation continues |

## 4. Choosing the right operator

Ask:
1. Are old results obsolete? Use switchMap.
2. Can operations run independently? Use mergeMap.
3. Must each operation finish in order? Use concatMap.
4. Should repeated triggers be ignored while busy? Use exhaustMap.

Do not choose based only on operator popularity. The business rule determines the correct behavior.

## 5. Error handling

Place catchError inside the inner stream when one failed request should not terminate the outer event stream:

```ts
searchTerms$.pipe(
  switchMap(term => this.http.get('/api/search', { params: { term } }).pipe(
    catchError(() => of([]))
  ))
);
```

If catchError is outside switchMap and returns a replacement Observable, the original outer stream may complete after the first error.

## 6. Common mistakes

- Using mergeMap for search and showing stale responses.
- Using switchMap for writes that must all complete.
- Using concatMap without considering queue growth.
- Expecting exhaustMap to queue ignored clicks.
- Catching errors outside the flattening operator and unintentionally ending the UI event stream.
- Assuming unsubscribe rolls back a server-side operation.

## 7. Interview questions and answers

**Q1. switchMap versus mergeMap?**  
switchMap keeps only the latest inner subscription. mergeMap allows multiple inner subscriptions concurrently.

**Q2. When use concatMap?**  
When every operation must run sequentially and in source order.

**Q3. When use exhaustMap?**  
When new triggers should be ignored while the current inner operation is active, such as blocking repeated form submissions.

**Q4. Does switchMap cancel an HTTP request?**  
It unsubscribes from the previous inner Observable. Angular HttpClient can abort the client-side request, but server work may already have started.

**Q5. Why put catchError inside switchMap?**  
It handles one request failure while keeping the outer event stream alive for future user actions.

## 8. Practice

Implement:
1. Search field using debounceTime + switchMap.
2. Multiple independent file uploads using mergeMap with a concurrency limit.
3. Sequential saves using concatMap.
4. Login button using exhaustMap to ignore double clicks.

## 9. Quick revision

switchMap = latest wins. mergeMap = concurrent. concatMap = sequential queue. exhaustMap = ignore while busy.
