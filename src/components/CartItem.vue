<script setup>
  defineProps({
    imageUrl: String,
    title: String,
    price: Number,
    id: Number,
    isAdded: Boolean,
    onClickAdded: Function,
  });
</script>

<template>
  <div
      class="flex w-full items-center border border-slate-200 p-4 rounded-xl gap-4"
  >
    <img :src="imageUrl" alt="sneaker" class="w-16 h-16"/>
    <div class="flex flex-col w-full">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2 grow">
        <strong>{{ price }} $.</strong>
        <img
            class="opacity-40 hover:opacity-100 cursor-pointer transition"
            src="/close.svg"
            @click="onClickAdded"
        />
      </div>
    </div>
  </div>
</template>
