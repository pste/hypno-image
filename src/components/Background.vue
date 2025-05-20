<script setup>
import {  onMounted, onUnmounted, ref, reactive, computed } from 'vue'

// image render stuff
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

// screen lock handler
let wakeLock = null;

async function createLock() {
    try {
        wakeLock = await navigator.wakeLock.request("screen");
        console.log("Wake Lock is active");
    } catch (err) {
        // The Wake Lock request has failed - usually system related, such as battery.
        console.error(`${err.name}, ${err.message}`);
    }
}
async function releaseLock() {
    await wakeLock.release();
    wakeLock = null;
    console.log("Wake Lock released");
};

// setup animation timer
let timer = null;
function startAnimation() {
    console.log("Animation started ...");
    if (timer) {
        clearInterval(timer)
    }
    timer = window.setInterval(function () {
        imgx.value = imgx.value + incx.value;
        imgy.value = imgy.value + incy.value;

        if (imgx.value > 500) incx.value = -1;
        else if (imgx.value < -500) incx.value = 1;
        if (imgy.value > 300) incy.value = -1;
        else if (imgy.value < -300) incy.value = 1;

        styleObject.backgroundPosition
        //console.log(imgx.value + " " + imgy.value)
    }, 100)
}

function stopAnimation() {
    clearInterval(timer);
    timer = null;
    console.log("Animation stopped.");
}

//
onMounted(() => {
    startAnimation();
    createLock();
})

onUnmounted(() => {
    stopAnimation();
    releaseLock();
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
