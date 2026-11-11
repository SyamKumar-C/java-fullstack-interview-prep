# Angular

> **Priority:** P1 | **Level:** Intermediate | **Study time:** 3–4 hours

Angular is a TypeScript framework for building web applications. Learn its component model, dependency injection, forms, routing, HTTP, change detection, and reactive programming.

## 1. Components and standalone components

A component combines a TypeScript class, template, and styles. Standalone components can import their dependencies directly and do not require an NgModule declaration.

```ts
@Component({
  selector: 'app-greeting',
  standalone: true,
  template: '<h2>Hello, {{ name }}</h2>'
})
export class GreetingComponent {
  name = 'Angular';
}
```

## 2. Interpolation, property binding, event binding

- Interpolation: `{{ value }}` displays a value.
- Property binding: `[disabled]="isSaving"` sets a DOM or component property.
- Event binding: `(click)="save()"` listens for an event.
- Two-way binding combines input and output binding, commonly written `[(ngModel)]` when using template-driven forms.

## 3. Directives and pipes

- Structural control flow/directives change which views are rendered, such as @if and @for in modern Angular templates.
- Attribute directives change behavior or appearance, such as ngClass.
- Pipes transform values for display, such as date and currency.

## 4. Inputs, outputs, and lifecycle

Inputs pass data into a component; outputs communicate events to a parent. Lifecycle hooks include ngOnInit, ngOnChanges, ngAfterViewInit, and ngOnDestroy. Use cleanup for subscriptions and external resources.

## 5. Dependency injection

Angular's injector provides services. Services are commonly marked with @Injectable. Provider scope determines where an instance is created, such as application-wide or component-level.

## 6. Routing and lazy loading

Routes map URLs to components. Lazy loading reduces initial bundle size by loading features when needed. Route guards control navigation decisions but are not a replacement for server-side authorization.

## 7. Reactive forms

Reactive forms model form state in TypeScript using FormControl, FormGroup, and FormArray. They are useful for complex validation and dynamic forms.

```ts
form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email])
});
```

## 8. HttpClient and interceptors

HttpClient returns Observables. Interceptors can add authentication headers, handle common errors, or add tracing metadata. Do not blindly retry non-idempotent requests.

## 9. Change detection, OnPush, and signals

Change detection updates the view when application state changes. OnPush can reduce unnecessary checks when used with immutable state and observable/signal patterns. Signals provide reactive state primitives such as signal, computed, and effect.

## 10. Interview questions

??? question "What is the difference between interpolation and property binding?"
    Interpolation converts an expression to text in the template. Property binding assigns a value to a DOM or component property. Use property binding for boolean and non-string properties such as disabled.

??? question "What is the difference between a component and a directive?"
    A component is a directive with its own template. Directives add behavior or change how an element/view behaves.

??? question "What is the difference between template-driven and reactive forms?"
    Template-driven forms are primarily configured in the template and suit simpler forms. Reactive forms define controls and validation in TypeScript and suit complex or dynamic forms.

??? question "What does OnPush change detection do?"
    It lets Angular skip checks for a component subtree in situations where its inputs and other supported triggers have not changed. It works best with predictable immutable state and modern reactive patterns.

??? question "Why use lazy loading?"
    It splits feature code into separately loaded chunks so users do not need to download every feature during initial application startup.

## 11. Practice

1. Build a reactive form with custom validation.
2. Create a reusable input component with inputs and outputs.
3. Explain how an HTTP interceptor adds a token.
4. Compare OnPush change detection with signals.
