<script setup>
  defineProps({
    imageUrl: String,
    title: String,
    description: String
  });
</script>

<template>
  <div class="flex items-center flex-col text-center w-72 mx-auto">
    <img :src="imageUrl" alt="image info" height="80" width="80"/>
    <h2 class="mt-5 text-2xl font-medium">{{ title }}</h2>
    <p class="text-gray-400">{{ description }}</p>
  </div>
</template>