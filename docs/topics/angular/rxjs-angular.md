# RxJS in Angular and Subscription Management

An Observable emits zero or more values, then completes or errors. It is lazy by default; subscription starts work.

Subject multicasts new values. BehaviorSubject has a current value and emits it to new subscribers. ReplaySubject replays configured history. AsyncSubject emits the last value only on completion.

Operators: map/filter transform/select; switchMap cancels prior inner stream; mergeMap runs concurrent inner work; concatMap queues in order; exhaustMap ignores new triggers while busy; debounceTime/distinctUntilChanged reduce repeated search; catchError recovers; takeUntilDestroyed ties subscriptions to Angular lifecycle.

Use async pipe for templates. For imperative long-lived subscriptions use takeUntilDestroyed or a destroy Subject pattern. HttpClient streams normally complete after one response.

**Interview:** switchMap vs mergeMap? Latest inner stream vs concurrent inner streams.
