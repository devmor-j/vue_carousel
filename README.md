# Vue Carousel

Advanced Carousel component writtin in [Vue 3](https://vuejs.org/) and [TypeScript](https://www.typescriptlang.org/)

Two Carousel components:

- [BaseCarousel](src/components/BaseCarousel.vue) for general use with [Vue's slot](https://vuejs.org/guide/components/slots.html) support
- [ImageCarousel](src/components/ImageCarousel.vue) specifically designed for image slideshow (has a prop to get all image paths)

## Carousel Features

Both Carousels have these features:

- Continuous item navigation
- Cycle through items
- Change cycle period (ex: '1000' in *ms*)
- Autofocus on page load
- Go to specific item using dots (pagination)
- Write custom html for prev/next buttons (default is arrow shape)
- Hide prev/next navigation buttons (arrows)
- Beautiful transitions
- Change transition time (ex: '0.3s')
- Adjust padding on each carousel item and whole carousel when in fullscreen mode (to prevent interference with carousel arrows)
- Excellent *touch* and *focus* support for navigation:
  - Cannot swipe items for now
- Keyboard support:
  - *Left arrow* goes to previous item
  - *Right arrow* goes to next item
  - *Escape* cancel focus (on carousel element)
  - *f* key view carousel in fullscreen mode
- Fullscreen mode (with Safari support) by:
  - Pressing *f* key (no interfere with *ctrl + f* which is binded to find action)
  - *Double clicking* on carousel (except it's arrow buttons, and pagination dots)
- Enough accessibility support

## Usage

### ImageCarousel

```html
<script setup lang="ts">
import { reactive } from "vue";
import ImageCarousel from "@/components/ImageCarousel.vue";

// your images should be an array of objects like below (alt is optional)
const state = reactive({
  carouselImages: [
    { path: "/images/1.jpg", alt: "aerial view island" },
    { path: "/images/2.jpg", alt: "annular eclipse sunset" },
    // and so forth...
  ],
});
</script>

<template>
  <main>
    <!-- use image carousel in your html -->
    <!-- don't need "section" tag, it's already wrapped inside one -->
    <image-carousel :images="state.carouselImages" />
  </main>
</template>
```

### BaseCarousel

*BaseCarousel* is desigend using slots for general usage. Simply put your html elements inside *BaseCarousel* and it's *BaseCarouselItem* like so:

```html
<script setup lang="ts">
import { reactive } from "vue";
import BaseCarousel from "./components/BaseCarousel.vue";
import BaseCarouselItem from "./components/BaseCarouselItem.vue";

const state = reactive({
  carouselContent: [
    {
      heading: "Heading",
      paragraph:
        "Lorem ipsum dolor sit amet consectetur adipisicing elit. Numquam, perferendis?",
      button: "Button",
    },
    {
      heading: "Heading",
      paragraph:
        "Lorem ipsum dolor, sit amet consectetur adipisicing elit. Nesciunt pariatur magnam molestiae?",
      button: "Button",
    },
    // and so forth...
  ],
});
</script>

<template>
  <main>
    <!-- total-items prop is required, everything else is optional -->
    <!-- you have to extract "current" index from base carousel -->
    <base-carousel
      v-slot="{ current }"
      :total-items="state.carouselContent.length"
    >
    <!-- v-show is required and works with "current" and some index to compare (in this case "i" from v-for) -->
    <!-- you can remove accessibility (aria) labels if you don't need them -->
      <base-carousel-item
        v-for="(item, i) in state.carouselContent"
        :key="`base-carousel-item-${i}`"
        v-show="current === i"
        :aria-hidden="current !== i"
        :aria-label="`slide ${i + 1} of ${state.carouselContent.length}`"
      >
      <!-- everything inside here will work fine, this is just an example -->
        <h2>{{ item.heading }} {{ i + 1 }}</h2>
        <p>{{ item.paragraph }}</p>
        <button>{{ item.button }} {{ i + 1 }}</button>
      </base-carousel-item>
    </base-carousel>
  </main>
</template>
```

### All Props

These are for *BaseCarousel* and should work fine for *ImageCarousel* as well:

```ts
// totalItems is required everything else is optional
type Props = {
  totalItems: number;
  cycle?: boolean | number;
  continuous?: boolean;
  autofocus?: boolean;
  hideArrows?: boolean;
  fullscreenPadding?: boolean;
  transitionDuration?: string;
  maxWidth?: string;
  prevButton?: string;
  nextButton?: string;
  dotButton?: string;
  hideDots?: boolean;
  overlayDots?: boolean;
};
```

### Props Defaults

```ts
const props = withDefaults(defineProps<Props>(), {
  cycle: false,
  continuous: true,
  transitionDuration: "0.2s",
  maxWidth: "40rem",
  prevButton: "&#65513;",
  nextButton: "&#65515;",
  dotButton: "&#149;",
  overlayDots: false,
});
```

Please note that if a prop is passed to component without value like this:

```html
`<base-carousel autofocus />`
```

It means `:autofocus="true"`.

Also for auto cycle you can either pass 'cycle' prop to BaseCarousel (or ImageCarousel) to enable default behaviour like this:

```html
<!-- this will enable slideshow with default 1000ms period -->
`<base-carousel cycle />`
```

or set the time (in ms) like example below:

```html
<!-- this will enable slideshow with 2500ms period -->
`<base-carousel :cycle="2500" />`
```

> Note that `cycle` is disbaled by default (because some people find it annoying)

## Notes

### CSS Notes

- Responsive design, from *320px* screen (smart watch, phone, tablet) up-to *4K* screens (laptop, desktop, TV)
- **Images are not absolute-positioned**, so CSS *content flow* is not broken (this means that if you nest the carousel inside another element there should be no CSS break happening)
- Supports dark/light color-schemes and carousel colors react to user's system theme
- Has almost no CSS resets, so it should work fine inside any project

### Logic sorting method

All Vue projects logic code can be sorted in two ways:

- **a)** Sort base on normal vue sections:

For example all actions for *onMounted* are always in the same place and when a new feature is added, it is placed inside the same (previous) *onMounted* hook.

This makes features coupled together because you have to know what relates to what!

- **b)** Sort base on features:

For example I already have used *onMounted* for one feature, but my new feature also needs to run an action when component mounts (needs to run inside, *onMounted*); in this case we don't touch previous codes and simply write everything needed for this feature to work at the end of the last feature code.

This makes adding and debugging very easy, because all features code are possibly in the same place.

> This project's logic is sorted based on feature concerns not specifically Vue's concern
>
> This means that you can easily find and remove features you don't want or add new features with no hassle to touch current code

## Continuous Development

Next features will be:

- [ ] Navigation via *touch swipes*
- [x] Navigation with pagination (dots)
- [ ] Show multiple items on each page
- [ ] Add pause button helper when carousel cycles
- [ ] Add fullscreen button helper for better accessibility
- [ ] Support double touch for fullscreen mode
