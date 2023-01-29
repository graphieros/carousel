<script setup lang="ts">
  import { ref } from 'vue';
  import CustomCarousel from './components/CustomCarousel.vue';

  const items = ref(() => {
    const arr = []
    for(let i = 0; i < 20; i += 1) {
      arr.push(i)
    }
    return arr;
  })

</script>

<template>
    <CustomCarousel 
      background="white" 
      style="width:100%" 
      height="140px" 
      :use-width-scroll="true"
      :items="items()"
    >
    </CustomCarousel>
</template>

<style>
#app {
  width:100%;
  background: white;
}
body {
  background: white;
}
</style>
