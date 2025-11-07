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

### Data model
```typescript
type ResourceOptions<T, R> = PromiseResourceOptions<T, R> | StreamingResourceOptions<T, R>
```

```typescript
interface ResourceRef<T> extends WritableResource<T> {
  hasValue(this: T extends undefined ? this : never): this is ResourceRef<Exclude<T, undefined>>;
  hasValue(): boolean;
  destroy(): void;
  readonly override value: WritableSignal<T>;
  override set(value: T): void;
  override update(updater: (value: T) => T): void;
  override asReadonly(): Resource<T>;
  override reload(): boolean;
  readonly override status: Signal<ResourceStatus>;
  readonly override error: Signal<Error | undefined>;
  readonly override isLoading: Signal<boolean>;
}
```

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