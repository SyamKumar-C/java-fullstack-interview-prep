# Angular Reactive Forms

Reactive forms define the form model in TypeScript. FormControl tracks one value; FormGroup groups controls; FormArray manages dynamic lists.

```ts
form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email])
});
```

Use built-in validators or custom ValidatorFn. Cross-field validation belongs at FormGroup level. AsyncValidatorFn can validate remotely; consider debounce and cancellation.

valueChanges is an Observable. For autocomplete/search use debounceTime, distinctUntilChanged and switchMap. Use async pipe or takeUntilDestroyed for long-lived subscriptions.

**Interview:** setValue vs patchValue? Full structure required vs partial update.
