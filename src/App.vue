<script setup>
import contextInput from '@/components/contextInput.vue'
</script>

<template>
  <main class="w-full h-fit my-7 flex flex-col items-center justify-center font-roboto">
    <h3 class="text-4xl font-bold mb-10">Prompt<span class="text-purple-500">refiner</span></h3>
    <contextInput :height="5" :width="30" />
  </main>
</template>

<style scoped></style>
