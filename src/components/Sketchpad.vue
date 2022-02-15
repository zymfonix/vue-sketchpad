<template>
  <svg
      style="width: 100%; height: 100%; display: block; touch-action: none"
      ref="canvas"
      @mousedown="handlePointerDown"
      @mousemove="handlePointerMove"
      @mouseup="handlePointerUp"
  >
    <path v-for="(path, index) in pathsWithData" :key="`path${index}`" :d="path.path" :fill="path.color" />
    <path :d="pathData" :fill="color" />
  </svg>
</template>

<script>
import {getStroke} from 'perfect-freehand'
import Canvg from 'canvg';
import {getSvgPathFromStroke} from '../utils'

export default {
  emits: ['update:modelValue'],

  props: {
    color: {
      type: String,
      default() {
        return '#000'
      }
    },
    options: {
      type: Object,
      default() {
        return {};
      }
    },
    modelValue: {
      type: Array,
      default() {
        return []
      }
    }
  },

  data() {
    return {
      points: [],
      paths: [],
    }
  },

  computed: {
    allOptions() {
      return {
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
        ...this.options
      }
    },
    stroke() {
      return getStroke(this.points, this.allOptions);
    },

    pathData() {
      return getSvgPathFromStroke(this.stroke);
    },

    pathsWithData() {
      return JSON.parse(JSON.stringify(this.paths)).map(points => {
        points.path = getSvgPathFromStroke(getStroke(points.path, this.allOptions));
        return points;
      })
    }
  },

  methods: {
    getCanvasX(e) {
      if (!this.$refs.canvas) return e.pageX;
      return e.pageX - this.$refs.canvas.getBoundingClientRect().left - window.scrollX;
    },

    getCanvasY(e) {
      if (!this.$refs.canvas) return e.pageY;
      return e.pageY - this.$refs.canvas.getBoundingClientRect().top - window.scrollY;
    },

    handlePointerDown(e) {
      this.points = [[this.getCanvasX(e), this.getCanvasY(e), e.pressure]]
    },

    handlePointerMove(e) {
      if (e.buttons !== 1) return;
      this.points = [...this.points, [this.getCanvasX(e), this.getCanvasY(e), e.pressure]]
    },

    handlePointerUp() {
      let path = JSON.parse(JSON.stringify(this.points))
      this.paths.push({
        path,
        color: this.color.toString()
      });
      this.points = []
    },

    clear() {
      this.paths = [];
    },

    async export() {
      const canvas = document.createElement('canvas');
      const ctx = canvas.getContext('2d');
      canvas.width = this.$refs.canvas.clientWidth;
      canvas.height = this.$refs.canvas.clientHeight;
      let item = await Canvg.fromString(ctx, this.$refs.canvas.outerHTML);

      item.start();

      return canvas.toDataURL();
    },

  },

  watch: {
    paths: {
      deep: true,
      handler(value) {
        if (value !== this.modelValue) this.$emit('update:modelValue', value)
      }
    },

    modelValue: {
      deep: true,
      handler(value) {
        if (value !== this.paths) this.paths = value;
      }
    }
  }
};
</script>