# Angular Components, Templates and Lifecycle

A component combines a TypeScript class, template and styles. Standalone components import their dependencies directly.

```ts
@Component({ selector: 'app-greeting', standalone: true,
  template: `<h2>Hello {{ name }}</h2>` })
export class GreetingComponent { name = 'Angular'; }
```

Template binding: interpolation `{{value}}`, property `[disabled]="saving"`, event `(click)="save()"`, two-way `[(ngModel)]="name"` (FormsModule required).

Lifecycle hooks include ngOnChanges, ngOnInit, ngDoCheck, ngAfterContentInit, ngAfterViewInit and ngOnDestroy.

**Interview:** Constructor vs ngOnInit? Constructor initializes the class/DI; ngOnInit runs after Angular initializes input properties.
