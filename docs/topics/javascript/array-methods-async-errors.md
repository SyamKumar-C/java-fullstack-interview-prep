# JavaScript Array Methods, Async Errors and Browser Storage

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Array methods

| Method | Purpose | Mutates original? |
|---|---|---|
| map | Transform each element | No |
| filter | Keep matching elements | No |
| reduce | Accumulate a result | No |
| find | First matching element | No |
| some | At least one match? | No |
| every | All match? | No |
| forEach | Run callback | No |
| sort | Sort elements | Yes |
| reverse | Reverse order | Yes |
| splice | Add/remove elements | Yes |
| slice | Copy a range | No |

```ts
const prices = [100, 250, 80];
const discounted = prices
  .filter(price => price >= 100)
  .map(price => price * 0.9);
const total = discounted.reduce((sum, price) => sum + price, 0);
```

Default sort is lexicographic for values converted to strings. Use a numeric comparator. Use toSorted where supported or sort a copy to avoid mutation:

```ts
const sorted = [...prices].sort((a, b) => a - b);
```

## 2. Async errors
Promise chains use catch:

```ts
fetch('/api/orders')
  .then(response => {
    if (!response.ok) throw new Error('Request failed');
    return response.json();
  })
  .catch(error => console.error('Could not load orders', error));
```

With async/await:

```ts
async function loadOrders() {
  try {
    const response = await fetch('/api/orders');
    if (!response.ok) throw new Error('Request failed');
    return await response.json();
  } catch (error) {
    // log safely and show a useful user message
    throw error;
  }
}
```

An async function always returns a Promise. Throwing inside it rejects that Promise. Catch must surround the awaited call to catch its rejection.

## 3. Browser storage

| Storage | Lifetime | Typical use |
|---|---|---|
| localStorage | Until cleared | Small preferences |
| sessionStorage | Current tab session | Temporary tab state |
| IndexedDB | Persistent until cleared/evicted | Larger structured/offline cache |
| Cookies | Configurable expiry | Server-related state |

localStorage is synchronous string storage. IndexedDB is asynchronous and supports structured data. Cookies are sent with matching requests depending on attributes. Do not treat browser storage as a secure vault. HttpOnly cookies reduce JavaScript access, but cookie-based authentication still needs CSRF protection and suitable SameSite/Secure settings.

## 4. Common mistakes
- Assuming map changes the source array.
- Using sort without numeric comparator.
- Forgetting splice mutates.
- Catching errors and silently returning invalid fallback.
- Treating localStorage as secure token storage.
- Forgetting async functions always return Promises.

## 5. Interview questions
**Q1. map vs forEach?** map returns a new array; forEach returns undefined.

**Q2. filter vs find?** filter returns all matches; find returns first match or undefined.

**Q3. Does sort mutate?** Yes; use toSorted or sort a copy.

**Q4. What does async function return?** Always a Promise.

**Q5. localStorage vs sessionStorage?** localStorage persists until cleared; sessionStorage is scoped to a tab session.

## 6. Practice
1. Find second-highest distinct number without mutating input.
2. Group orders by status using reduce.
3. Explain why [2, 10, 1].sort() is not numeric order.
4. Choose storage for a preference versus offline cache.
