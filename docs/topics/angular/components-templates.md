# Angular Components, Templates and Lifecycle

## 1. What is a component?

A component is the basic UI building block in Angular. It combines a TypeScript class (data and behavior), an HTML template (view), and optional styles. Angular creates a component instance and keeps its view in sync with its state.

A standalone component declares its dependencies in its own `imports`. This is the recommended approach for modern Angular applications.

## 2. Creating a component

```ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-greeting',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h2>Hello {{ name }}</h2>
    <button [disabled]="saving" (click)="save()">Save</button>
  `
})
export class GreetingComponent {
  name = 'Angular';
  saving = false;

  save(): void {
    this.saving = true;
  }
}
```

The selector is how the component is used in another template: `<app-greeting />`.

## 3. Template binding types

| Binding | Syntax | Purpose |
|---|---|---|
| Interpolation | `{{ value }}` | Display a value as text |
| Property | `[disabled]="saving"` | Set a DOM or component property |
| Event | `(click)="save()"` | React to an event |
| Two-way | `[(ngModel)]="name"` | Keep view and model in sync |

Two-way binding with `ngModel` requires `FormsModule`. For complex forms, Reactive Forms are usually easier to test and control.

## 4. Inputs and outputs

Use inputs to pass data from parent to child and outputs to notify the parent. Modern Angular supports signal-based `input()` and `output()`; decorator-based `@Input` and `@Output` are also common in existing applications.

```ts
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <span>{{ userName() }}</span>
    <button (click)="selected.emit(userName())">Select</button>
  `
})
export class UserCardComponent {
  userName = input.required<string>();
  selected = output<string>();
}
```

Parent: `<app-user-card [userName]="name" (selected)="onSelected($event)" />`.

## 5. Lifecycle hooks

| Hook | When it runs | Typical use |
|---|---|---|
| `ngOnChanges` | When data-bound inputs change; first call before `ngOnInit` | React to input changes |
| `ngOnInit` | Once after initial inputs are set | Initial component setup |
| `ngDoCheck` | During each change-detection check | Custom change checking; use sparingly |
| `ngAfterContentInit` | Once after projected content initializes | Read content queries |
| `ngAfterContentChecked` | After projected content is checked | Rare custom checks |
| `ngAfterViewInit` | Once after the component view initializes | Read view queries |
| `ngAfterViewChecked` | After the view is checked | Rare custom checks |
| `ngOnDestroy` | Just before destruction | Cleanup subscriptions/listeners |

`DestroyRef` and `takeUntilDestroyed()` are useful modern cleanup options.

## 6. Constructor vs ngOnInit

The constructor is JavaScript/TypeScript object construction. Angular dependency injection is available there, but input properties are not yet guaranteed to be initialized. `ngOnInit` runs after Angular has assigned initial inputs.

Do not put expensive work or input-dependent logic in the constructor.

## 7. Content projection

Use `ng-content` to let a parent provide content to a child component.

```html
<!-- child template -->
<section class="panel">
  <ng-content />
</section>

<!-- parent template -->
<app-panel><p>Projected content</p></app-panel>
```

Projection is useful for reusable cards, dialogs and layout components.

## 8. Practical scenarios

- Reusable table: parent passes rows and column configuration; child emits row selection.
- Dialog: parent controls visibility and passes data; dialog emits save/cancel events.
- Cleanup: unsubscribe from long-lived streams or register cleanup with `DestroyRef`.

## 9. Common mistakes

- Forgetting to import a directive, pipe or child component in a standalone component.
- Using `ngOnInit` when the logic must react to later input changes; use `ngOnChanges` or an input signal effect.
- Updating state repeatedly in checked lifecycle hooks and causing change-detection loops.
- Forgetting to clean up manual subscriptions and event listeners.
- Using two-way binding everywhere instead of clear one-way data flow.

## 10. Interview questions

**Q1. What is a component?** A class, template and styles that define one part of the UI.

**Q2. Constructor vs ngOnInit?** Constructor creates the class and receives dependencies; ngOnInit runs after initial inputs are assigned.

**Q3. What is the difference between input and output?** Input sends data from parent to child; output sends an event from child to parent.

**Q4. What is content projection?** A child template provides a slot using `ng-content`, and the parent supplies the content.

**Q5. When does ngOnDestroy run?** Before Angular destroys the component/directive instance.

## 11. Practice

Build a reusable employee card with a required name input, an active status input, and a selected output. Add a parent that renders several cards and handles selection.

## 12. Quick revision

Component = class + template + styles. Know four binding forms, parent-child communication, lifecycle order, standalone imports and cleanup.
