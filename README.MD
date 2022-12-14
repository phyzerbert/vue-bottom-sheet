![Example](https://anyprinter.ru/logo.jpg)

# Vue Bottom Sheet
![Size](https://img.shields.io/bundlephobia/minzip/@phyzerbert/vue-bottom-sheet)
![Downloads](https://img.shields.io/npm/dt/@phyzerbert/vue-bottom-sheet)
![Version](https://img.shields.io/npm/v/@phyzerbert/vue-bottom-sheet)

A nice clean and touch-friendly bottom sheet component based on [Vue.js](https://vuejs.org/) and [Hammer.js](https://hammerjs.github.io/)

 - :clapper: [Demo](https://phyzerbert.github.io/vue-bottom-sheet-demo/)

## Installation

### NPM

`npm install --save @phyzerbert/vue-bottom-sheet`

### Yarn

`yarn add @phyzerbert/vue-bottom-sheet`

## Usage

```vue
<template>
  <vue-bottom-sheet ref="myBottomSheet">
    <h1>Lorem Ipsum</h1>
    <h2>What is Lorem Ipsum?</h2>
    <p>
      <strong>Lorem Ipsum</strong> is simply dummy text
    </p>
  </vue-bottom-sheet>
</template>

<script>
import  VueBottomSheet from "@phyzerbert/vue-bottom-sheet";

export default {
  components: {
    VueBottomSheet
  },
  methods: {
    open() {
      this.$refs.myBottomSheet.open();
    },
    close() {
      this.$refs.myBottomSheet.close();
    }
  }
}
</script>
```

Or add to main.js for use throughout the project
```js
import VueBottomSheet from "@phyzerbert/vue-bottom-sheet";
import Vue from "vue";

Vue.use(VueBottomSheet);
```

## Props

| Prop  | Type | Description | Example |
| ------------- | ------------- | ------------- | ------------- |
| max-width  | String  | Set max-width of component card  | `max-width="640px"` |
| max-height  | String  | Set max-height of component card  | `max-height="90%"` |
| min-height  | Number  | Set min-height of component card when closed  | `:min-height="50"` |
| effect  | String  | Set effect for component card  | `effect="fx-fadein-scale"` |
| rounded  | Boolean  | Round the top two corners of the sheet  | `:rounded="false"` |
| is-full-screen  | Boolean  | Enable or disable full-screen mode  | `:is-full-screen="true"` |
| swipe-able  | Boolean  | Enable or disable swipe to close | `:swipe-able="false"` |
| background-scrollable  | Boolean  | Enable scroll | `:background-scrollable="true"` |
| swipe-limit  | Number  | Set swipe end limit pixels  | `:swipe-limit="50"` |

## Events

| Event  | Description | Example |
| ------------- | ------------- | ------------- |
| opened  | Fire when card component is opened  | @opened="" |
| closed  | Fire when card component is closed  | @closed="" |

### List of effects

- fx-default
- fx-fadein-scale
- fx-slide-from-right
- fx-slide-from-left

You can see all the effects on the [demo page](https://phyzerbert.github.io/vue-bottom-sheet-demo/)
