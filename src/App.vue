<script setup>
import Sketchpad from './components/Sketchpad.vue'
import {ref} from "vue";

const pad = ref('pad')
const color = ref('#000');
const exportedPad = ref(null);

const changeColor = (newColor) => {
  color.value = newColor
}

const exportPad = () => {
  pad.value.export().then(value => {
    exportedPad.value = value;
  })
}
</script>

<template>
  <div>
    <button class="colorButton" style="background-color: #000" @click="changeColor('#000')"></button>
    <button class="colorButton" style="background-color: #ff0000" @click="changeColor('#ff0000')"></button>
    <button class="colorButton" style="background-color: #00FF00" @click="changeColor('#00FF00')"></button>
    <button class="colorButton" style="background-color: #0000ff" @click="changeColor('#0000FF')"></button>
    <button @click="$refs.pad.clear()">Clear</button>
    <button @click="exportPad">Export</button>
  </div>
  <div class="preview" v-if="exportedPad">
    <img :src="exportedPad" alt="">
  </div>
  <div class="sketchpad">
    <Sketchpad ref="pad" :color="color" />
  </div>
</template>


<style>
html,
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

body {
  user-select: none;
  background-color: #000;
  padding-right: 80px;
}

.preview {
  position: fixed;
  right: 0;
  background: #fff;
  top: 0;
  width: 500px;
}

.preview img {
  width: 100%;
}

.sketchpad {
  width: 100%;
  max-width: 800px;
  height: 800px;
  background-color: #efefef;
}

.colorButton {
  width: 50px;
  height: 50px;
  display: inline-block;
  border: none;
}

</style>