# Event Loop, Promises and async/await

JavaScript runs synchronous code on a call stack. Async work is coordinated through host APIs and queues. Promise callbacks run as microtasks; timers typically schedule tasks/macrotasks. After the current stack completes, microtasks are drained before the next task (host details vary).

## Promise
A Promise is pending, fulfilled or rejected and settles once. then() returns a new Promise; thrown errors become rejections.

## Combinators
Promise.all fulfills when all fulfill and rejects on first rejection. allSettled waits for every outcome. race settles with the first settled input. any fulfills with the first fulfillment, or rejects with AggregateError if all reject.

## async/await
async functions always return a Promise. await suspends that async function's continuation, not the JavaScript thread.

```js
async function load() {
  try {
    const response = await fetch('/api/orders');
    if (!response.ok) throw new Error('Request failed');
    return await response.json();
  } catch (error) {
    console.error(error);
    throw error;
  }
}
```

**Interview:** Does await block the thread? No; it suspends the async function and schedules its continuation.
