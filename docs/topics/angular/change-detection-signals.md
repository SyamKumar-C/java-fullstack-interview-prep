# Angular Change Detection and Signals

## 1. Change detection

Angular change detection checks whether component state changes require the view to be updated. When Angular detects relevant changes, it evaluates bindings and updates the DOM where needed.

A component template might show `{{ count }}`. When count changes and Angular checks the component, the displayed value is refreshed.

## 2. Default and OnPush strategies

- Default: Angular checks the component as part of normal change-detection traversal.
- OnPush: Angular can skip a subtree until a relevant trigger occurs, such as a new input reference, an event in the subtree, a signal update read by the template, or an explicit mark/check.

OnPush is a performance tool, not a guarantee that a component is never checked. Avoid mutating an input object in place because the reference may remain the same.

```ts
@Component({
  selector: 'app-user',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `{{ user.name }}`
})
export class UserComponent {
  @Input() user!: User;
}
```

Prefer immutable updates:
```ts
this.user = { ...this.user, name: 'Asha' };
```

## 3. Zone-based change detection

Traditionally Angular used Zone.js to observe many asynchronous browser events and schedule change detection. Newer Angular versions also support zoneless approaches. The exact setup depends on the Angular version and application configuration.

Do not assume every async callback automatically triggers view updates in every change-detection mode.

## 4. Signals

A signal is a reactive value. Reading it registers a dependency; updating it notifies consumers that depend on it.

```ts
import { signal, computed, effect } from '@angular/core';

count = signal(0);
doubleCount = computed(() => this.count() * 2);

increment() {
  this.count.update(value => value + 1);
}
```

- signal: writable reactive state.
- computed: derived, read-only state; recalculates when dependencies change.
- effect: runs a side effect when signals it reads change. Use sparingly and do not use effects as a replacement for computed state.

In templates, read a signal by calling it: `{{ count() }}`.

## 5. Signals and RxJS

Signals are useful for synchronous state and derived values in the UI. RxJS is useful for streams over time, events, cancellation, and async composition. Angular provides interop utilities such as toSignal and toObservable in supported versions.

Example: an HTTP stream can be converted to a signal for template consumption, but the stream's lifecycle and error/loading behavior still need design.

## 6. Real scenario

A dashboard displays a selected customer and computed order total. Store selected customer in a signal; derive the total with computed. Use RxJS for search input debouncing and HTTP requests because those are event streams with cancellation and timing concerns.

## 7. Common mistakes

- Mutating objects used as OnPush inputs without changing the reference.
- Using effect to copy one signal into another instead of computed.
- Reading a signal without calling it.
- Treating signals as a replacement for every RxJS use case.
- Putting expensive work directly in template expressions.

## 8. Interview questions and answers

**Q1. What is change detection?**  
The process Angular uses to check template bindings and update the DOM when application state changes.

**Q2. What does OnPush do?**  
It lets Angular skip checking a component subtree until relevant triggers occur, reducing unnecessary work.

**Q3. What is a signal?**  
A reactive container for a value. Consumers that read it are notified when it changes.

**Q4. Difference between signal and computed?**  
signal stores writable state; computed derives a read-only value from other signals.

**Q5. When use effect?**  
For side effects such as synchronizing with an external API or logging. Use computed for derived state.

**Q6. Signals versus RxJS?**  
Signals model reactive state and derived values. RxJS models asynchronous streams and provides operators for timing, combination, cancellation, and errors.

## 9. Practice

Build a counter with a computed double value. Then build a search field using RxJS debounceTime and switchMap, and explain why the two tools solve different problems.

## 10. Quick revision

OnPush reduces checks when inputs/state are stable. Signals track reactive state dependencies. computed derives; effect performs side effects. RxJS remains useful for async streams.
