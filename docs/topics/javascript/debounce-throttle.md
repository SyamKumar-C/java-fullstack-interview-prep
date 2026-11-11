# Debounce and Throttle

Debounce delays execution until a quiet period passes. Useful for search boxes and resize handling. Throttle limits execution frequency during continuous activity. Useful for scroll handlers.

```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

A production implementation may need leading/trailing options, cancellation, preserved this and maxWait.

**Interview:** Debounce vs throttle? Debounce waits for quiet; throttle limits frequency over time.
