# JavaScript Event Loop, Promises and async/await

## 1. JavaScript execution model

JavaScript commonly runs application code on one main thread. The call stack executes synchronous functions. The host environment (browser or Node.js) provides APIs for timers, network activity, and other work. Completed async work schedules callbacks or jobs for later execution.

The event loop coordinates when queued work runs after the current stack is clear.

## 2. Call stack and queues

- Call stack: currently executing function calls.
- Task/macrotask queue: tasks such as timer callbacks and many event callbacks.
- Microtask queue: Promise reactions and queueMicrotask callbacks.
- Rendering: browsers may update the screen between tasks according to their scheduling rules.

After the current synchronous task finishes, the runtime drains microtasks before moving to a later task. Browser rendering and host details have additional rules.

## 3. Example: output order

```js
console.log('A');

setTimeout(() => console.log('timer'), 0);

Promise.resolve().then(() => console.log('promise'));

console.log('B');
```

Output:
```
A
B
promise
timer
```

The synchronous logs run first. Promise callbacks are microtasks; the timer callback is a later task. A zero-millisecond timer does not mean it runs immediately.

## 4. Promise states and chaining

A Promise is pending, fulfilled, or rejected. It settles once. `then` returns a new Promise, which allows chaining and error propagation.

```js
fetch('/api/users')
  .then(response => {
    if (!response.ok) throw new Error('HTTP error');
    return response.json();
  })
  .then(users => console.log(users))
  .catch(error => console.error(error));
```

A thrown error in a then callback rejects the Promise returned by that then. A catch can recover by returning a value, or continue rejection by throwing again.

## 5. async and await

An async function always returns a Promise. Await pauses that async function's continuation until the awaited value settles; it does not block the JavaScript thread.

```js
async function loadUsers() {
  try {
    const response = await fetch('/api/users');
    if (!response.ok) throw new Error('Request failed');
    return await response.json();
  } catch (error) {
    console.error(error);
    throw error;
  }
}
```

Awaiting a non-Promise value is allowed; it behaves like awaiting an already fulfilled value.

## 6. Sequential versus parallel work

Sequential:
```js
const user = await getUser();
const orders = await getOrders(user.id);
```

Parallel when independent:
```js
const [users, products] = await Promise.all([getUsers(), getProducts()]);
```

Promise.all rejects as soon as one input rejects, but it does not automatically cancel the other operations. Use AbortController or an API-specific cancellation mechanism when cancellation is needed.

## 7. Promise.all, allSettled, race and any

- Promise.all: fulfills when all fulfill; rejects when one rejects.
- Promise.allSettled: waits for every input to settle and returns each status.
- Promise.race: settles with the first input to settle.
- Promise.any: fulfills with the first fulfilled input; rejects with AggregateError if all reject.

Choose based on whether partial failures are acceptable and whether the first completion or first success matters.

## 8. Common mistakes

- Assuming setTimeout(..., 0) runs before Promise callbacks.
- Using await inside a loop when independent work could run in parallel.
- Using Promise.all when each result must succeed independently.
- Forgetting to return a Promise from a then callback.
- Assuming Promise.race cancels losing operations.
- Swallowing errors in catch and returning undefined unintentionally.

## 9. Interview questions and answers

**Q1. What is the event loop?**  
The runtime mechanism that schedules queued callbacks after the current call stack is clear.

**Q2. Microtask versus task?**  
Promise reactions are microtasks and are processed before the next task in the common browser event-loop model. Timer callbacks are tasks.

**Q3. Does await block the thread?**  
No. It suspends the async function's continuation and allows other work to run.

**Q4. What does an async function return?**  
Always a Promise. A returned value fulfills it; a thrown error rejects it.

**Q5. Promise.all versus allSettled?**  
all rejects on the first rejection; allSettled waits for all results and reports each outcome.

**Q6. Does Promise.race cancel the other promises?**  
No. It only adopts the first settled result. Cancellation must be handled separately.

## 10. Practice

Predict the output of code mixing synchronous logs, setTimeout, queueMicrotask, and Promise.then. Then write an async function that fetches two independent resources in parallel and handles partial failure with allSettled.

## 11. Quick revision

Synchronous stack first; microtasks drain before the next task. async functions return Promises. await suspends a function, not the thread. Choose Promise combinators based on failure behavior.
