<script setup>
import {  onMounted, onUnmounted, ref, reactive, computed } from 'vue'

// params
const animSpeed = 30;   // ms
const screenOn = 20;    // secs
const screenOff = 60;   // secs
const imgurl = "url('/pattern-3.png')";

// component data
const imgx = ref(0);
const imgy = ref(0);
const incx = ref(-1);
const incy = ref(-1);
const bgImage = ref(imgurl);

const bgImagePosition = computed(() => {
    return imgx.value + "px " + imgy.value + "px";
})

const styleObject = reactive({
  backgroundPosition: bgImagePosition,
  backgroundImage: bgImage,
})



// events
function clicked() {
    if (timerScreenSaver) {
        stopSaverAnimation();
    }
    else {
        bgImage.value = imgurl;
        startSaverAnimation();
    }
}

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

// setup animation for screen saver
let timerScreenSaver = null;
function startSaverAnimation() {
    console.log("Screensaver started ...");
    let on = true;
    let count = 0;
    //
    if (timerScreenSaver) {
        clearInterval(timerScreenSaver)
    }
    timerScreenSaver = window.setInterval(function () {
        count++;
        if (on && count >= screenOn) {
            on = false;
            count = 0;
            bgImage.value = null;
        }
        if (!on && count >= screenOff) {
            on = true;
            count = 0;
            bgImage.value = imgurl;
        }
        //
    }, 1000); // every sec
}

function stopSaverAnimation() {
    clearInterval(timerScreenSaver);
    timerScreenSaver = null;
    console.log("Screensaver stopped.");
}

// setup animation timer for bg image
let timerBG = null;
function startBgAnimation() {
    console.log("Animation started ...");
    if (timerBG) {
        clearInterval(timerBG)
    }
    timerBG = window.setInterval(function () {
        imgx.value = imgx.value + incx.value;
        imgy.value = imgy.value + incy.value;

        if (imgx.value > 500) incx.value = -1;
        else if (imgx.value < -500) incx.value = 1;
        if (imgy.value > 300) incy.value = -1;
        else if (imgy.value < -300) incy.value = 1;

        //console.log(imgx.value + " " + imgy.value)
    }, animSpeed)
}

function stopBgAnimation() {
    clearInterval(timerBG);
    timerBG = null;
    console.log("Animation stopped.");
}

//
onMounted(() => {
    startBgAnimation();
    startSaverAnimation();
    createLock();
})

onUnmounted(() => {
    stopBgAnimation();
    stopSaverAnimation();
    releaseLock();
})
</script>

<template>
    <div 
        class="bg" 
        :style="styleObject"
        @click="clicked"
    > </div><!--@touchstart="clicked"-->
</template>

<style scoped>
.bg {
  height: 100vh;
  width: 100vw;
  background-repeat: repeat;
}
</style>
