# Angular Directives, Pipes and Dependency Injection

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Short interview answer
A component is a directive with a template. Attribute directives change an existing element's behavior or appearance; structural directives add or remove views. Pipes transform values for display. Dependency injection supplies services according to injector scope.

## 2. Directive types
A component combines class, template, and metadata. An attribute directive changes an existing element:

```ts
@Directive({ selector: '[appHighlight]', standalone: true })
export class HighlightDirective {
  private el = inject(ElementRef<HTMLElement>);
  private renderer = inject(Renderer2);

  @HostListener('mouseenter')
  onEnter() {
    this.renderer.setStyle(this.el.nativeElement, 'backgroundColor', '#fff3cd');
  }

  @HostListener('mouseleave')
  onLeave() {
    this.renderer.removeStyle(this.el.nativeElement, 'backgroundColor');
  }
}
```

Use Renderer2 for DOM changes where practical. Do not insert untrusted HTML. Structural directives change rendered views. Modern Angular has built-in `@if`, `@for`, and `@switch`; older templates commonly use `*ngIf` and `*ngFor`.

## 3. Pipes
Built-in pipes:

```html
<p>{{ total | currency:'INR' }}</p>
<p>{{ createdAt | date:'dd-MMM-yyyy' }}</p>
```

Custom pipe:

```ts
@Pipe({ name: 'initials', standalone: true })
export class InitialsPipe implements PipeTransform {
  transform(name: string | null): string {
    return (name ?? '').trim().split(/\s+/)
      .filter(Boolean)
      .map(part => part[0].toUpperCase())
      .join('');
  }
}
```

Pure pipes run when primitive input or object reference changes. Impure pipes can run on every change-detection cycle and hurt performance. Prefer pure pipes and immutable updates.

## 4. Dependency injection

```ts
@Injectable({ providedIn: 'root' })
export class UserService {}

@Component({
  selector: 'app-profile',
  standalone: true,
  template: '...'
})
export class ProfileComponent {
  private users = inject(UserService);
}
```

Provider scope:
- providedIn root: usually one shared application-level instance.
- Component providers: instance scoped to that component subtree.
- Route providers: scoped to route injector.
- Application/environment providers: configured during bootstrap.

A component-level provider creates a separate instance for that subtree. Be deliberate with stateful services.

## 5. Common mistakes
- Using impure pipes for expensive filtering.
- Mutating an object in place and expecting a pure pipe to detect it.
- Providing stateful service at component level when shared state is required.
- Direct DOM manipulation when Angular bindings are enough.
- Assuming client-side code is safe for server-side rendering without checking browser globals.

## 6. Interview questions
**Q1. Component vs directive?** A component is a directive with a template.

**Q2. Attribute vs structural directive?** Attribute changes an existing element; structural adds/removes views.

**Q3. Pure vs impure pipe?** Pure runs when input/reference changes; impure may run every change-detection cycle.

**Q4. What happens when a service is provided in a component?** That component subtree gets its own provider instance.

**Q5. Why use DI?** It separates object creation from use and makes dependencies replaceable/testable.

## 7. Practice
1. Build a permission-based attribute directive.
2. Compare pure pipe behavior for immutable and in-place updates.
3. Provide a service at root and component level; compare instances.
