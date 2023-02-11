<template>
  <div class="flex flex-col flex-1">
    <!-- Анимация задержки для общего прогноза-->
    <div class="flex flex-col py-12 items-center">
      <AnimatedPlaceholder class="max-w-[300px] w-full mb-2" />
      <AnimatedPlaceholder class="max-w-[300px] w-full mb-12" />
      <AnimatedPlaceholder class="max-w-[300px] h-[100px] w-full mb-12" />
      <AnimatedPlaceholder class="max-w-[300px] w-full mb-8" />
      <AnimatedPlaceholder class="max-w-[300px] h-[75px] w-full" />
    </div>
    <!-- Анимация задержки для почасового прогноза -->
    <div class="flex flex-col py-12 px-8 items-center">
      <AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full mb-12" />
    </div>
    <!-- Анимация задержки для недельного прогноза -->
    <div class="flex flex-col py-12 px-8 items-center">
      <AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full mb-12" />
    </div>
  </div>
</template>

<script setup>
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>
