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

---
layout: two-cols-header
---

# async event with `resource()`
### Data model 

:: left ::

<v-click>

Resource

```typescript
interface Resource<T> {
  readonly value: Signal<T>;
  readonly status: Signal<ResourceStatus>;
  readonly error: Signal<Error | undefined>;
  readonly isLoading: Signal<boolean>;
  hasValue(this: T extends undefined ? this : never)
  : this is Resource<Exclude<T, undefined>>;
  hasValue(): boolean;
}
```
</v-click>

<v-click>

ResourceStatus

```typescript
type ResourceStatus = 'idle' 
| 'error' 
| 'loading' 
| 'reloading' 
| 'resolved' 
| 'local'
```

</v-click>


:: right ::

<v-click>

WritableResource

```typescript
interface WritableResource<T> extends Resource<T> {
  readonly value: WritableSignal<T>;
  hasValue(this: T extends undefined ? this : never)
  : this is WritableResource<Exclude<T, undefined>>;
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
</v-click>



---
layout: default
---

# async event with `resource()`
### Example

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

---
layout: default
---

# async event with `resource()`
## `httpResource()`

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
</v-click>
<v-click>

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

<v-click>

Is dirty?
```typescript
form().dirty()
```
</v-click>
<v-click>

Is touched?
```typescript
form().touched()
```

</v-click>
<v-click>

Is valid or invalid?
```typescript
form().valid()
form().invalid()
```
</v-click>

---
layout: quote
---

# signal forms is an expertimental feature

---
layout: section
---

# state management

---
layout: center
---

# state management
## *@ngrx/signals*

- `signalState()`
- `signalStore()`

---
layout: section
---

# `signalState()`

---
layout: default
---

# state management
## signalState()
### example

````md magic-move

```typescript {*}
import { signalState } from '@ngrx/signals';
```

```typescript {*}
import { signalState } from '@ngrx/signals';

type Book = {
  title: string;
  author: string;
}
```

```typescript {*}
import { signalState } from '@ngrx/signals';

type Book = {
  title: string;
  author: string;
}
type BookState = { book: Book; };

const bookState = signalState<BookState>({
  book: { title: 'The black swan', author: 'Nassim Taleb' },
});
```

````

---
layout: section
---

# `signalStore()`

---

# state management
## signalStore()
### basic example

````md magic-move 
```typescript

import { signalStore, withState } from '@ngrx/signals';

type Book = {
  title: string;
  author: string;
}

type BookSearchState = {
  books: Book[];
  isLoading: boolean;
  filter: { query: string; order: 'asc' | 'desc' };
};

const initialState: BookSearchState = {
  books: [],
  isLoading: false,
  filter: { query: '', order: 'asc' },
};

export const BookSearchStore = signalStore(
  withState(initialState)
);
```
````

---

# state management
## signalStore()
### basic example - providing

````md magic-move 
```typescript
// local providing
providers: [BookSearchStore]
```
```typescript
// global providing
export const BookSearchStore = signalStore(
  { providedIn: 'root' }
  withState(initialState)
);
```
```typescript
// providing with injection token
const BOOK_SEARCH_STATE = new InjectionToken<BookSearchState>(
  'BookSearchState',
  { factory: () => initialState }
);
```

```typescript
// local providing
providers: [BookSearchStore]
// global providing
export const BookSearchStore = signalStore(
  { providedIn: 'root' }
  withState(initialState)
);
// providing with injection token
const BOOK_SEARCH_STATE = new InjectionToken<BookSearchState>(
  'BookSearchState',
  { factory: () => initialState }
);
```

````
---

# state management
## signalStore()
### a real-world example

````md magic-move 
```typescript {|1,16|}

import { signalStore, withState, withComputed } from '@ngrx/signals';

type Book = { /**/ }

type BookSearchState = { /**/ };

const initialState: BookSearchState = {
  books: [],
  isLoading: false,
  filter: { query: '', order: 'asc' },
};

export const BookSearchStore = signalStore(
  withState(initialState),
  withComputed({ /**/ })
);
```
```typescript
export const BookSearchStore = signalStore(
  withState(initialState),
  withComputed({ /**/ })
);
```
```typescript
export const BookSearchStore = signalStore(
  withState(initialState),
  withComputed(({books}) => { 
    total: computed(() => books().length)
  })
);
```
```typescript
export const BookSearchStore = signalStore(
  withState(initialState),
  withComputed(({books}) => { 
    total: computed(() => books().length)
  }),
  withMethod((store) => {
    updateQuery(query: string): void {
      patchState(store, (state) => ({ filter: { ...state.filter, query } }));
    },
    load: rxMethod<string>(
      // load the data from a data source
    )
  })
);
```
```typescript

@Component({
  template: `
    <h1>Books ({{ total() }})</h1>

    <ul>
      @for (book of books(); track $index) {
        <li>{{ book.title }} - {{ book.author }}</li>
      }
    </ul>
  `,
  providers: [BookSearchStore],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class BookSearch {
//
}


```

```typescript

@Component({
//
})
export class BookSearch {
  readonly #store = inject(BookSearchStore);
  readonly books: Signal = this.store.books;
  readonly total: Signal = this.store.total;

  constructor() {
    const query = this.store.filter.query;
    // 👇 Re-fetch books whenever the value of query signal changes.
    this.store.load(query);
  }
}


```

````

---
layout: center
---

# put everything together

---
layout: default
---

# state management
## put everything together
### complete example

````md magic-move 

```typescript
@Component({
  imports: [Control],
  template: `
    <input [control]="form.title" placeholder="Title"/>
    <input [control]="form.author" placeholder="Author"/>
  `,
  providers: [BookStore],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class BookForm {
//
}

```

```typescript
@Component({
  imports: [Control],
  template: `
    <input [control]="form.title" placeholder="Title"/>
    <input [control]="form.author" placeholder="Author"/>
  `,
  providers: [BookStore],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class BookForm {
  readonly #store = inject(BookStore);
  readonly form = form(this.#store.form);
}

```

```typescript
@Component({
  imports: [Control],
  template: `
    <input [control]="form.title" placeholder="Title"/>
    <input [control]="form.author" placeholder="Author"/>
  `,
  providers: [BookStore],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class BookForm {
  readonly #store = inject(BookStore);
  readonly form = form(this.#store.form);
}

const bookStore = signalStore(
  withState({title: '', author: ''})
  withComputed((state) => { /* */ }),
  withMethod((store) => {
    save(formData: { title: string; author: string }): void {      /* save data */    },
    load: rxMethod<string>( /* load the data from a data source */)
  })
)
```

````

---
layout: center
---

# can we ditch rxjs?

---
layout: center
---

# rxjs interoperability

<v-clicks>

- `toSignal()`

- `toObservable()`

- `rxResource()`

</v-clicks>

---
layout: default
---

# rxjs interoperability
## toSignal()
### example

```typescript
//snippet
readonly obs = interval(1000)

readonly signal = toSignal(obs, { initialValue: 0 });

```

---
layout: default
---

# rxjs interoperability
## toObservable()
### example

```typescript
//snippet
readonly sig = signal(0)

readonly obs = toObservable(sig);

//set a new value, obs will emit a new value
sig.set(1)
```