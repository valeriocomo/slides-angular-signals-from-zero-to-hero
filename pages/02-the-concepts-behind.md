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
````md magic-move

```typescript {*}
@Component({
    template: `
    <p>{{ numericValue$ | async }}</p>
    <button (click)="divideByTwo()" [disabled]="disableDivideByTwo$ | async">Divide by two</button>
    <button (click)="doubleIt()">Double</button>
    `,
    imports: [AsyncPipe]
})
export class DoubleComponent {
    readonly numericValue$ = new BehaviourSubject(1);
    readonly disableDivideByTwo$ = this.numericValue$.pipe(
        map(v => v <= 1)
    );

    divideByTwo() {
        const numericValue = this.numericValue$.value / 2;
        this.numericValue$.next(numericValue);
    }

    doubleIt() {
        const numericValue = this.numericValue$.valu * 2;
        this.numericValue$.next(numericValue);
    }
}
```

```typescript
@Component({
    template: `
    <p>{{ numericValue() }}</p>
    <button (click)="divideByTwo()" [disabled]="disableDivideByTwo()">Divide by two</button>
    <button (click)="doubleIt()">Double</button>
    `
})
export class DoubleComponent {
    readonly numericValue = signal(1);
    readonly disableDivideByTwo = computed(() => this.numericValue() <= 1);

    divideByTwo() {
        this.numericValue.update(c => c / 2);
    }

    doubleIt() {
        this.numericValue.update(c => c * 2);
    }
}
```
````