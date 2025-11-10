---
layout: image
image: '/images/fuerte-1.jpg'
preload: true
transition: slide-up
---

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <h1>Signalsification in Angular</h1> 
    </div>
</div>

---
layout: center
---

# Signalsification in Angular

- async event with `resource()`

- signal forms

- state management

---
layout: section
---

# async event with `resource()`

---
layout: center
---

# fetch data from a server


---
layout: default
---

# async event with `resource()`
### API 

```typescript
function resource<T, R>(
  options: ResourceOptions<T, R> & { defaultValue: NoInfer<T> },
): ResourceRef<T>;
```

<v-click>

### Data model

```typescript
type ResourceOptions<T, R> = PromiseResourceOptions<T, R> | StreamingResourceOptions<T, R>
```

</v-click>

<!--   -->

---
layout: default
---

# async event with `resource()`
## Example

```typescript {|1|2-7|3|4-6|}
const bookId: Signal<string> = getBookId();
const bookResource: ResourceRef = resource({
  params: () => ({id: bookId()}),
  loader: ({params, abortSignal, previous}): Promise<Book> => {
    return fetch(`books/${params.id}`, {signal: abortSignal});
  },
});
// ...
bookResource.reload();
```

<v-click>

```typescript 
type ResourceStatus = 'idle' | 'error' | 'loading' | 'reloading' | 'resolved' | 'local'
```

</v-click>

---
layout: default
---

# async event with `resource()`
### Data model 

```typescript
interface Resource<T> {
  readonly value: Signal<T>;
  readonly status: Signal<ResourceStatus>;
  readonly error: Signal<Error | undefined>;
  readonly isLoading: Signal<boolean>;
  hasValue(this: T extends undefined ? this : never): this is Resource<Exclude<T, undefined>>;
  hasValue(): boolean;
}
```

```typescript
interface WritableResource<T> extends Resource<T> {
  readonly value: WritableSignal<T>;
  hasValue(this: T extends undefined ? this : never): this is WritableResource<Exclude<T, undefined>>;
  hasValue(): boolean;
  set(value: T): void;
  update(updater: (value: T) => T): void;
  asReadonly(): Resource<T>;
  reload(): boolean;
  readonly override status: Signal<ResourceStatus>;
  readonly override error: Signal<Error | undefined>;
  readonly override isLoading: Signal<boolean>;
}
```

---
layout: default
---

# async event with `resource()`
## httpResource()

It's a wrapper around HttpClient

<v-click>

### TS

```typescript
const bookSchema = z.object({
  title: z.string(),
  authore: z.string(),
});

bookId = input.required<string>();
book = httpResource(() => `/api/books/${bookId()}`, { parse: bookSchema.parse });
```

### HTML

```html
@if(book.hasValue()) {
  <book-details [book]="book.value()">
} @else if (book.error()) {
  <div>Could not load book information</div>
} @else if (book.isLoading()) {
  <div>Loading book info...</div>
}
```

</v-click>

---
layout: section
---

# signal forms

---
layout: default
---

# signal forms
### API 

```typescript
function form<TModel>(
  model: WritableSignal<TModel>,
  schema: SchemaOrSchemaFn<TModel>,
  options: FormOptions,
): FieldTree<TModel>;
```

<v-click>

### example 

TS
```typescript
protected readonly data = signal({name: '', surname:''});
protected readonly form = form(this.data);
```

</v-click>

<v-click>

HTML
```html
<input [control]="form.name" placeholder="Name"/>
<input [control]="form.surname" placeholder="Surname"/>
```

</v-click>


---
layout: default
---

# signal forms
## complete example 

````md magic-move
```typescript {*}{lines:false,startLine:1}
import { ChangeDetectionStrategy, Component, signal } from '@angular/core';
import { Control, form, minLength, required } from '@angular/forms/signals';
```
```typescript {*}{lines:false,startLine:3}
@Component({
  selector: 'app-form',
  imports: [Control],
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <input [control]="form.name" placeholder="Name" />
    <input [control]="form.surname" placeholder="Surname" />
    <hr>
    @let nameErrors = form.name().errors();
    @for (error of nameErrors; track $index) {
      <li>{{error.message}}</li>
    } 
    <button [disabled]="form().invalid()" (click)="submit()">SUBMIT</button>
  `,
})
```
```typescript {*}{lines:false,startLine:14}
export class FormComponent {
  protected readonly data = signal({ name: '', surname: '', email: ''})
  protected readonly form = form(this.data, (p) => {
    required(p.name, { message: 'Name is required' });
    required(p.surnmame, { message: 'Surname is required' });
    minLength(p.surname, 2, { message: 'too short' })
  });

  submit() {
    console.log(this.form().value());
  }
}
```
````

---
layout: default
---

# signal forms
## form state

### API
````md magic-move
```typescript
interface FieldState<TValue, TKey extends string | number = string | number> extends ɵFieldState<TValue> {
  readonly dirty: Signal<boolean>;
  readonly hidden: Signal<boolean>;
  readonly disabledReasons: Signal<readonly DisabledReason[]>;
  readonly errors: Signal<WithField[]>;
  readonly errorSummary: Signal<WithField[]>;
  readonly valid: Signal<boolean>;
  readonly invalid: Signal<boolean>;
  readonly pending: Signal<boolean>;
  readonly submitting: Signal<boolean>;
  readonly keyInParent: Signal<TKey>;
  readonly fieldBindings: Signal<readonly Field<unknown>[]>;
  //...
}
```

```typescript
interface FieldState<TValue, TKey extends string | number = string | number> extends ɵFieldState<TValue> {
  //...
  readonly override disabled: Signal<boolean>;
  readonly override max?: Signal<number | undefined> | undefined;
  readonly override maxLength?: Signal<number | undefined> | undefined;
  readonly override min?: Signal<number | undefined> | undefined;
  readonly override minLength?: Signal<number | undefined> | undefined;
  readonly override name: Signal<string>;
  readonly override pattern: Signal<readonly RegExp[]>;
  readonly override readonly: Signal<boolean>;
  readonly override required: Signal<boolean>;
  readonly override touched: Signal<boolean>;
  readonly override value: WritableSignal<T>;
  override markAsDirty(): void;
  override markAsTouched(): void;
}
```
````

---
layout: default
---

# signal forms
## form state

Is dirty?
```typescript
form().dirty()
```

Is touched?
```typescript
form().touched()
```

Is valid or invalid?
```typescript
form().valid()
form().invalid()
```


---
layout: section
---

# state management
