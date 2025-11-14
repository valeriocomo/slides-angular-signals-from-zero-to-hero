---
# You can also start simply with 'default'
theme: apple-basic
layout: intro-image
image: '/images/cover-intro.png'
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Angular Signals from zero to hero
info: |
  ## Angular Signals: from zero to hero
# apply unocss classes to the current slide
# class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
seoMeta:
  # By default, Slidev will use ./og-image.png if it exists,
  # or generate one from the first slide if not found.
  ogImage: auto
  # ogImage: https://cover.sli.dev
---

# Angular Signals
# From zero to hero 🚀​

<div class="absolute bottom-10">
  <span class="font-700">
    Valerio Como
  </span>
</div>

<div class="abs-br m-6 text-xl">
  <a href="https://github.com/valeriocomo/slides-angular-signals-from-zero-to-hero" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

---
src: ./pages/00-about-me.md
transition: fade
---

---
src: ./pages/01-agenda.md
---

---
src: ./pages/02-the-concepts-behind.md
---

---
src: ./pages/03-signals-api.md
---

---
src: ./pages/04-signalsification-in-angular.md
---

---
src: ./pages/05-wrap-up.md
---