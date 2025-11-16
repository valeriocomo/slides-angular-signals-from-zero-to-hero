---
layout: image
image: '/images/signals-api.png'
preload: true
transition: slide-up
---

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <h1>signals API</h1> 
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
layout: section
---

# signal()

---
layout: default
---

# signals API
## signal()

### API

```typescript {|4}
function signal<T>(
    initialValue: T,
  options?: CreateSignalOptions<T> | undefined,
): WritableSignal<T>;
```

---
layout: default
---

# signals API
## signal()
### example

```typescript {|10|10,13|10,13,17}

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

---
layout: section
---

# computed()

---
layout: default
---

# signals API
## computed()

### API

```typescript {|4}
function computed<T>(
  computation: () => T,
  options?: CreateComputedOptions<T> | undefined,
): Signal<T>;
```

---
layout: default
---

# signals API
## computed()
### example


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
 

---
layout: section
---

# effect()

---
layout: default
---

# signals API
## effect()

### API

```typescript
function effect(
  effectFn: (onCleanup: EffectCleanupRegisterFn) => void,
  options?: CreateEffectOptions | undefined,
): EffectRef;
```

---
layout: default
---

### EXAMPLE

```typescript {|13-15}
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


---
layout: center
---

# signals API
## effect()
### use cases

- logging
- data sync with (local|session)Storage
- DOM behaviour
- performing ops with 3rd party UI library

---
layout: center
---

# you can only create an `effect()` within an injection context

---
layout: image
image: images/captain-holt.gif
---

---
layout: default
---

# signals API
## effect()
### injection context

```typescript {|7|3|8,12-14|4,18-20|3,4,7,8,12-14,18-20}
export class AppComponent {
    // other data
    readonly #privateLogger: EffectRef = effect(() => console.log(`privateLogger: counter is ${this.counter()}`));
    readonly #injector = inject(Injector);

    constructor() {
        effect(() => console.log(`constructor: counter is ${this.counter()}`));
        initLogging();
    }

    #initLogging() {
        effect(() => {
            console.log(`initLogging: counter is ${this.counter()}`)
        })
    }

    #someOtherMethod() {
        effect(() => {
            console.log(`someOtherMethod: counter is ${this.counter()}`)
        }, {injector: this.#injector})
    }
}
```

---
layout: default
---

# signals API
## effect()
### destroy effects

<v-clicks>

- destroyed with its context
 
- calling `destroy()`

</v-clicks>

<v-click>

```typescript
export class AppComponent {
    // other code here
    private readonly privateLogger: EffectRef = effect(
        () => console.log(`privateLogger: counter is ${this.counter()}`)
    );

    // other methods here
    private destroyLogging() {
        this.privateLogger.destroy();
    }
    // other methods here
}
```
</v-click>

---
layout: section
---

# linkedSignal()

---
layout: default
---

# signals API
## linkedSignal()


### API

```typescript

function linkedSignal<D>(
  computation: () => D,
  options?:
    | {
        equal?: ValueEqualityFn<NoInfer<D>> | undefined;
        debugName?: string | undefined;
      }
    | undefined,
): WritableSignal<D>;

```

---
layout: default
---

# signals API
## linkedSignal()

### example

```typescript

const rhcpOptions = signal(
    [
        'John Frusciante', 
        'Anthony Kiedis', 
        'Flea',
        'Chad Smith'
    ]);
const selectedOption = linkedSignal(() => rhcpOptions()[0]);

console.log(selectedOption()); // 'John Frusciante'

selectedOption.set(rhcpOptions()[2]);

console.log(selectedOption()); // 'Flea'
// john left
rhcpOptions.set(['Dave Navarro', 'Anthony Kiedis', 'Flea', 'Chad Smith']);

console.log(selectedOption()); // 'Dave Navarro'
```
