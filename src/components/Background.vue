<script setup>
import {  onMounted, onUnmounted, ref, reactive, computed } from 'vue'

const imgx = ref(0);
const imgy = ref(0);
const incx = ref(-1);
const incy = ref(-1);

const bgImage = computed(() => {
    return imgx.value + "px " + imgy.value + "px";
})

const styleObject = reactive({
  backgroundPosition: bgImage,
})

let timer;
onMounted(() => {
    timer = window.setInterval(function () {
        imgx.value = imgx.value + incx.value;
        imgy.value = imgy.value + incy.value;

        if (imgx.value > 500) incx.value = -1;
        else if (imgx.value < -500) incx.value = 1;
        if (imgy.value > 300) incy.value = -1;
        else if (imgy.value < -300) incy.value = 1;

        styleObject.backgroundPosition
        console.log(imgx.value + " " + imgy.value)
    }, 100)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>

<template>
  <div class="bg" :style="styleObject">
  </div>
</template>

<style scoped>
.bg {
  height: 100vh;
  width: 100vw;
  /*background-position: center;
  background-repeat: no-repeat;
  background-size: cover;*/
  background-repeat: repeat;
  background-image: url('/pattern-2.png');
}
</style>
