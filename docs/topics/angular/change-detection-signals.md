# Angular Change Detection and Signals

Angular checks template-bound values and updates the DOM. Default strategy checks broadly; OnPush narrows checks based on supported triggers such as changed inputs, events, signals and explicit marking.

```ts
count = signal(0);
doubleCount = computed(() => this.count() * 2);
increment() { this.count.update(v => v + 1); }
```

signal is writable state; computed is derived read-only state; effect runs side effects when tracked signals change and should be used sparingly.

RxJS models streams over time and is strong for async events/cancellation/composition. Signals model current state and derived values. Angular uses both.

**Interview:** Does OnPush mean change detection never runs? No; it changes when Angular checks a component.
