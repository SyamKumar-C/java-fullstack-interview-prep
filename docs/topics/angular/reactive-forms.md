# Angular Reactive Forms

## 1. What are reactive forms?

Reactive forms define form state and validation in TypeScript. The component owns a FormGroup/FormControl model, while the template binds inputs to that model.

They are useful for complex forms, dynamic controls, cross-field validation, predictable testing, and programmatic updates.

## 2. FormControl, FormGroup and FormArray

- FormControl: one field and its value/status.
- FormGroup: a named set of controls.
- FormArray: an ordered dynamic list of controls.

```ts
form = new FormGroup({
  name: new FormControl('', { nonNullable: true, validators: [Validators.required] }),
  email: new FormControl('', [Validators.required, Validators.email]),
  skills: new FormArray<FormControl<string>>([])
});
```

Typed forms help catch mistakes at compile time. Use nonNullable when a control should not reset to null.

## 3. Template binding

```html
<form [formGroup]="form" (ngSubmit)="submit()">
  <input formControlName="name">
  <input formControlName="email">
  <button type="submit" [disabled]="form.invalid">Save</button>
</form>
```

## 4. Validators

Built-in validators include required, min, max, minLength, maxLength, pattern, and email. Validators return null when valid or an error object when invalid.

```ts
const passwordMatch: ValidatorFn = group => {
  const password = group.get('password')?.value;
  const confirm = group.get('confirmPassword')?.value;
  return password === confirm ? null : { passwordMismatch: true };
};
```

Attach cross-field validation to the FormGroup, not to one individual field.

## 5. Custom validators

A validator is a function that accepts an AbstractControl and returns ValidationErrors or null.

```ts
function noWhitespace(control: AbstractControl): ValidationErrors | null {
  const value = String(control.value ?? '');
  return value.trim().length ? null : { whitespace: true };
}
```

For async validation, return a Promise or Observable. Use it for checks such as username availability, and avoid sending a request on every keystroke without suitable update/debounce behavior.

## 6. setValue versus patchValue

- setValue requires the complete structure for the group.
- patchValue updates only supplied controls.

Use setValue when the complete form shape must be present; patchValue when applying a partial update.

## 7. value, getRawValue, disabled controls

Disabled controls are excluded from a FormGroup's aggregate value. Use getRawValue when you need disabled control values too. Do not disable a field only to hide it if its value must still be submitted without understanding this behavior.

## 8. updateOn and status

A control can update on change, blur, or submit using updateOn. Controls expose statuses such as VALID, INVALID, PENDING, and DISABLED. Async validators may put a control into PENDING.

## 9. Dynamic forms

Use FormArray for repeated fields, such as multiple phone numbers. Add/remove controls through the form model rather than directly changing the template.

## 10. Common mistakes

- Mixing template-driven and reactive form patterns unnecessarily.
- Using setValue with a partial object.
- Forgetting disabled controls are omitted from form.value.
- Writing a cross-field validator on the wrong control.
- Showing validation errors before the user has interacted, without a clear UX rule.
- Forgetting to unsubscribe from custom long-lived subscriptions.

## 11. Interview questions and answers

**Q1. What is a reactive form?**  
A form whose structure, values, and validation are defined in TypeScript and bound to the template.

**Q2. FormGroup versus FormArray?**  
FormGroup contains named controls; FormArray contains an ordered dynamic collection.

**Q3. setValue versus patchValue?**  
setValue expects the full form structure; patchValue updates only supplied fields.

**Q4. How do you implement cross-field validation?**  
Attach a ValidatorFn to the FormGroup and compare the relevant child controls.

**Q5. Why use typed forms?**  
They improve type safety for control values and reduce runtime mistakes.

**Q6. Why is a control PENDING?**  
An async validator is still running.

## 12. Practice

Create a registration form with required name, valid email, password, confirm password, and a dynamic FormArray of phone numbers. Add a group-level password match validator and explain setValue/patchValue behavior.

## 13. Quick revision

FormControl = field; FormGroup = named controls; FormArray = dynamic list. setValue is complete; patchValue is partial. Cross-field validation belongs on the group.
