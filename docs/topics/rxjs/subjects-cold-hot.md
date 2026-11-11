# Subjects, Cold and Hot Observables

Subject multicasts new values to active subscribers. BehaviorSubject stores a current value and emits it immediately to new subscribers. ReplaySubject replays configured history. AsyncSubject emits its last value only when it completes.

Cold Observable starts its producer separately per subscriber. Hot Observable shares a producer. Two subscriptions to a cold HTTP Observable can cause two requests.

share and shareReplay can share a source. Configure shareReplay bufferSize/refCount intentionally to avoid keeping expensive sources alive or retaining stale data.

**Interview:** Subject vs BehaviorSubject? BehaviorSubject has a current value and emits it immediately to new subscribers.
