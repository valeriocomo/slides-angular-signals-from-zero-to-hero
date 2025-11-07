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

- signalForms

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
