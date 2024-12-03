<script setup lang="ts">
import { ref } from 'vue';

const imageUrls = ref<string[]>([])
const currentImage = ref<number>(0)

const getUrls = async () => {
  let res = await (await fetch("/images.json")).json() as {from?: string, to?: string, url: string}[];
  res = res.filter( (v) => {
    if (!v.from || !v.to) return true

    return Date.parse(v.from) < Date.now() && Date.parse(v.to) > Date.now()
  })
  imageUrls.value = res.map(x => x.url)
}

// Get new images
getUrls()
setInterval(() => {
  getUrls()
}, 120000)

// change current image
setInterval(() => {
  if (currentImage.value + 1 >= imageUrls.value.length)
    currentImage.value = 0
  else 
    currentImage.value += 1
}, 10000)

</script>

<template>
  <main>
    <div v-if="imageUrls.length > 0" class="image-container">
      <img :src="imageUrls[currentImage]">
    </div>
  </main>
</template>

<style scoped>

img {
  transform: rotate(90deg);
  transform-origin:bottom left;
  
  position:absolute;
  top:-100vw;
  
  height:100vw;
  width:100vh;
}

main {
  /* position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0; */
  background-color: black;
}

.image-container {

}
</style>