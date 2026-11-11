# JavaScript Debounce and Throttle

## 1. Why rate-limit events?

Events such as keyup, scroll and resize can fire many times in a short period. Running expensive work for every event can waste CPU and trigger too many API calls.

Debounce and throttle control how often a function runs, but they solve different problems.

## 2. Debounce

Debounce waits until calls stop for a configured delay, then runs once using the latest arguments.

```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

const search = debounce(query => fetchResults(query), 300);
```

Use it for search input: wait until the user pauses typing before making a request.

## 3. Throttle

Throttle allows a function to run at most once per time window (depending on leading/trailing configuration).

```js
function throttle(fn, delay) {
  let lastRun = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastRun >= delay) {
      lastRun = now;
      fn.apply(this, args);
    }
  };
}
```

Use it for scroll or resize handlers where periodic updates are enough.

## 4. Comparison

| Debounce | Throttle |
|---|---|
| Runs after activity pauses | Runs at a controlled maximum rate |
| Good for search typing | Good for scroll/resize updates |
| Continuous events can keep postponing execution | Continuous events still allow periodic execution |

Production implementations often support leading and trailing calls, cancellation and flushing.

## 5. RxJS equivalents

- `debounceTime(300)`: emit after a quiet period.
- `throttleTime(300)`: limit emissions during a time window.
- `auditTime` and `sampleTime`: other event-sampling strategies.

Choose based on whether you need the latest value after a pause or periodic updates during activity.

## 6. Practical Angular example

```ts
this.searchControl.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(q => this.api.search(q ?? ''))
).subscribe(results => this.results = results);
```

For component code, also handle errors and subscription cleanup.

## 7. Common mistakes

- Using debounce for scroll progress when periodic updates are needed.
- Forgetting to preserve this and arguments in a reusable wrapper.
- Not cancelling a pending debounce timer when the component is destroyed.
- Confusing rate limiting in the browser with server-side rate limiting/security.

## 8. Interview questions

**Q1. Debounce vs throttle?** Debounce waits for a pause; throttle limits execution frequency while events continue.

**Q2. Which is suitable for autocomplete?** Debounce, commonly combined with switchMap.

**Q3. Which is suitable for scroll tracking?** Throttle or an RxJS sampling operator, depending on desired behavior.

**Q4. Do these protect the backend from abuse?** No. Server-side rate limiting is still required.

## 9. Practice

Implement both utilities. Test a burst of ten calls within 100 ms and explain how many times each function runs.
