# Vue Sketchpad

Vue3 wrapper around the [Perfect Freehand library](https://github.com/steveruizok/perfect-freehand).

It takes the points collected and creates a SVG element that acts as the canvas.

Each touch/swipe is added as a new path to the SVG, which gives you the ability to implement undo-action.

## Usage

### Basic example

```vue

<template>
  <Sketchpad ref="pad" v-model="paths" />
</template>
<script>
import Sketchpad from "@zymfonix/vue-sketchpad";

export default {
  components: {Sketchpad},

  data() {
    return {
      paths: []
    }
  }
}
</script>
```

You can change [the options](https://github.com/steveruizok/perfect-freehand#options) for the stroke by passing in
an `options` object to the component. This will merge with the component defaults.

```js
export default {
    size: 4,
    thinning: 0.5,
    smoothing: 0.5,
    streamline: 0.5,
    easing: (t) => t,
    start: {
        taper: 0,
        easing: (t) => t,
        cap: true
    },
    end: {
        taper: 100,
        easing: (t) => t,
        cap: true
    },
}
```

To change the color of the next path you're writing you can add a `color` prop to the component. This needs to a
CSS-readable color as it is added to the path directly.

### Export

To export the contents of your sketchpad you may call the `export` method on the component. This will resolve a promise
with a Base64 encoded image string.

```js
this.$pad.export().then(imageString => {
    // do something with imageString
})
```