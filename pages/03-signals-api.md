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

---
layout: two-cols-header
---

# signals API
## signal

:: left ::

### API

<v-click>
```typescript {|4|}
function signal<T>(
    initialValue: T,
  options?: CreateSignalOptions<T> | undefined,
): WritableSignal<T>;
```
</v-click>

:: right ::

<v-click>

### example

```typescript {}
@Component({
    template: `
    <p>{{ counter() }}</p>
    <button (click)="decrease()">-</button>
    <button (click)="increase()">+</button>
    `
})
export class AppComponent {
    readonly counter: WritableSignal = signal(0);
    
    decrease() {
        this.counter.update(c => c-1);
    }

    increase() {
        this.counter.update(c => c+1);
    }
}
```
</v-click>
---
layout: two-cols-header
---

# signals API
## computed

:: left ::

### API

```typescript {|4|}
function computed<T>(
  computation: () => T,
  options?: CreateComputedOptions<T> | undefined,
): Signal<T>;
```

:: right :: 

<v-click>

### example

</v-click>

<v-click>

```typescript {|4,10}
@Component({
    template: `
    <p>{{ counter() }}</p>
    <button (click)="decrease()" [disabled]="disableDecrease()">-</button>
    <button (click)="increase()">+</button>
    `
})
export class AppComponent {
    readonly counter: WritableSignal = signal(0);
    readonly disableDecrease: Signal = computed(() => this.counter() <= 0);

    decrease() {
        this.counter.update(c => c-1);
    }

    increase() {
        this.counter.update(c => c+1);
    }
}
```
</v-click>
 

---
layout: two-cols-header
---

# signals API
## effect

:: left ::

### API

```typescript
function effect(
  effectFn: (onCleanup: EffectCleanupRegisterFn) => void,
  options?: CreateEffectOptions | undefined,
): EffectRef;
```

:: right ::

<v-click>

### EXAMPLE

```typescript {|13-15|}
@Component({
    template: `
    <p>{{ counter() }}</p>
    <button (click)="decrease()" [disabled]="disableDecrease()">-</button>
    <button (click)="increase()">+</button>
    `
})
export class AppComponent {
    readonly counter: WritableSignal = signal(0);
    readonly disableDecrease: Signal = computed(() => this.counter() <= 0);

    constructor() {
        effect(() => 
            console.log(`the value of counter is ${this.counter()}`)
        );
    }

    decrease() {
        this.counter.update(c => c-1);
    }

    increase() {
        this.counter.update(c => c+1);
    }
}
```

</v-click>

---
layout: center
---

# signals API
## effect
### use cases

- logging
- data sync with (local|session)Storage
- DOM behaviour
- performing ops with 3rd party UI library

---
layout: center
---

# you can only create an effect() within an injection context

---
layout: image
image: images/captain-holt.gif
---

---
layout: default
---

# signals API
## effect
### injection context

```typescript {|9-11|4-6|13,16-20}
export class AppComponent {
    readonly counter: WritableSignal = signal(0);
    readonly disableDecrease: Signal = computed(() => this.counter() <= 0);
    private readonly privateLogger = effect(() => 
        console.log(`privateLogger: counter is ${this.counter()}`)
    );

    constructor() {
        effect(() => 
            console.log(`constructor: counter is ${this.counter()}`)
        );

        initLogging();
    }

    private initLogging() {
        effect(() => {
            console.log(`initLogging: counter is ${this.counter()}`)
        })
    }
    // other methods
}
```