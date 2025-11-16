---
layout: image
image: '/images/the-concept-behind.png'
preload: true
transition: slide-up
---

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <h1>the concepts behind</h1> 
    </div>
</div>

---
layout: center
---
# the concepts behind

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <v-clicks>

🤟​ easy-to-use reactive primitive

🔍 fine-graind change detection

​🫶​​ easier API than RxJS
 
</v-clicks>  
    </div>
</div>

---
layout: section
---

# 🤟 easy-to-use reactive primitive

---
layout: center
---

<div class="slidev-layout h-full grid section">
    <div class="my-auto">
        <h1>reactive?</h1>
    </div>
</div>

---
layout: iframe
url: https://en.wikipedia.org/wiki/Reactive_programming
---

---
layout: image
image: images/the-office-1.gif
---


---
layout: section
---

# 🔍 fine-graind change detection


---
layout: image
image: images/Default-change-detection-1.gif
backgroundSize: contain
---

<div class="ml-3">
    <p style="color: black"><b>Default Change Detection</b></p>
</div>

---
layout: image
image: images/onPush-change-detection.gif
backgroundSize: contain
---

<div class="ml-3">
    <p style="color: black"><b>OnPush Change Detection</b></p>
</div>

---
layout: image
image: images/signal-change-detection.gif
backgroundSize: contain
---

<div class="ml-3">
    <p style="color: black"><b>Signals Change Detection</b></p>
</div>

---
layout: section
---

# ​🫶 easier API than RxJS

---
layout: quote
---

# the difference between Signals and RxJS is the same between a scalpel and a swiss knife

---
layout: full
---

```typescript
@Component({
    template: `
    <p>{{ counter | async }}</p>
    <button (click)="decrease()" [disabled]="disableDecrease | async">-</button>
    <button (click)="increase()">+</button>
    `,
    imports: [AsyncPipe]
})
export class AppComponent {
    readonly counter = new BehaviourSubject(0);
    readonly disableDecrease = this.counter.pipe(
        map(counter => counter <= 0)
    );

    decrease() {
        const counter = --this.counter.value;
        this.counter.next(counter);
    }

    increase() {
        const counter = ++this.counter.value;
        this.counter.next(counter);
    }
}
```

---
layout: full
---

```typescript
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