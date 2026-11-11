# JavaScript, TypeScript and RxJS

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 4–5 hours

## 1. Scope, hoisting, and closures

JavaScript scope can be global, function, or block scope. var is function-scoped; let and const are block-scoped. Hoisting describes how declarations are processed before execution, but let and const remain in the temporal dead zone until initialized.

A closure is a function together with access to variables from its surrounding lexical scope.

```js
function createCounter() {
  let count = 0;
  return () => ++count;
}
const next = createCounter();
console.log(next()); // 1
console.log(next()); // 2
```

## 2. this and prototypes

The value of this depends on how a function is called, except for arrow functions, which capture this lexically. JavaScript objects can inherit behavior through the prototype chain. Classes provide syntax over this prototype-based model.

## 3. Event loop and asynchronous work

JavaScript runs synchronous code on a call stack. Asynchronous work is coordinated through host APIs and queues. Promise callbacks use the microtask queue; timers schedule tasks/macrotasks. The event loop processes queued work when the stack is clear.

## 4. Promise and async/await

A Promise represents a future completion or failure. async functions return promises; await pauses that async function until the promise settles, without blocking the whole JavaScript thread.

```js
async function loadUser() {
  try {
    const response = await fetch('/api/user');
    if (!response.ok) throw new Error('Request failed');
    return await response.json();
  } catch (error) {
    console.error(error);
    throw error;
  }
}
```

## 5. TypeScript fundamentals

Know type annotations, interfaces, type aliases, unions, intersections, generics, narrowing, enums, and utility types such as Partial, Pick, Omit, and Record.

```ts
type ApiResult<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function unwrap<T>(result: ApiResult<T>): T {
  if (result.success) return result.data;
  throw new Error(result.error);
}
```

## 6. Observable vs Promise

A Promise produces one eventual result and starts when created. An Observable can produce zero, one, or many values over time and generally starts when subscribed to. Observables support operators and unsubscription.

## 7. Subjects

- Subject multicasts values to current subscribers.
- BehaviorSubject stores a current value and emits it to new subscribers.
- ReplaySubject replays a configured number/time window of previous values to new subscribers.

## 8. Higher-order mapping operators

- switchMap cancels the previous inner subscription when a new source value arrives; useful for search suggestions.
- mergeMap runs inner streams concurrently; useful when all independent results matter.
- concatMap queues inner streams and processes them sequentially; useful when order matters.
- exhaustMap ignores new source values while the current inner stream is active; useful to prevent repeated submit actions.

## 9. Combination and error operators

- combineLatest emits when any input changes after each has emitted once.
- forkJoin waits for all inputs to complete and emits their last values.
- zip pairs values by position.
- catchError replaces or rethrows an error stream.
- retry resubscribes after errors.
- debounceTime waits for a quiet period.
- shareReplay shares and replays emissions; configure lifecycle and ref-count behavior carefully.

## 10. Interview questions

??? question "What is a closure?"
    A closure is a function that retains access to variables from the lexical scope where it was created, even after that outer function has returned.

??? question "What is the difference between a Promise and an Observable?"
    A Promise resolves or rejects once. An Observable can emit multiple values over time, is commonly lazy until subscribed to, and supports cancellation through unsubscription.

??? question "switchMap vs mergeMap?"
    switchMap switches to the newest inner stream and unsubscribes from the previous one. mergeMap allows multiple inner streams to run concurrently.

??? question "forkJoin vs combineLatest?"
    forkJoin emits once after all inputs complete, using their last values. combineLatest emits whenever an input changes after every input has emitted at least once.

??? question "Why unsubscribe from an Observable?"
    Unsubscription stops the subscription and can release resources or cancel ongoing work. Some sources complete automatically, but long-lived streams may otherwise cause leaks.

## 11. Practice

1. Explain the output order of synchronous code, Promise.then, and setTimeout.
2. Implement search suggestions with debounceTime and switchMap.
3. Use forkJoin to load independent reference data.
4. Use concatMap for ordered writes and explain why mergeMap differs.
