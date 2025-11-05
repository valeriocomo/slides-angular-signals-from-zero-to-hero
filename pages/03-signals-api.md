---
layout: image
image: '/images/signals-api.png'
preload: true
transition: slide-up
---

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <h1>Signals API</h1> 
    </div>
</div>

---
layout: center
---

# signals API

- signal()

- computed()

- effect()

- linkedSignal()

---
layout: two-cols-header
---

# signals vs writeble signals

:: left ::

## signal

<v-click>

### A reactive value which notifies consumers of any changes.

</v-click>

<v-click>

```typescript
type Signal<T> = (() => T) & {
  [SIGNAL]: unknown;
}
```
</v-click>

:: right ::

## writableSignal

<v-click>

### A `Signal` with a value that can be mutated via a setter interface.

</v-click>

<v-click>


```typescript
interface WritableSignal<T> extends Signal<T> {
  set(value: T): void;
  update(updateFn: (value: T) => T): void;
  asReadonly(): Signal<T>;
  override [SIGNAL]: unknown;
}
```

</v-click>
