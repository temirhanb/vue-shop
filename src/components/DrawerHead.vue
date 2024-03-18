<script setup>
  import {inject} from "vue";

  const {viewDrawer} = inject("drawer");

</script>

<template>
  <div class="flex gap-5 items-center mb-4">
    <svg
        class="cursor-pointer rotate-180 hover:opacity-70 transition hover:-translate-x-1"
        fill="none"
        height="14"
        viewBox="0 0 16 14"
        width="16"
        xmlns="http://www.w3.org/2000/svg"
        @click="()=>viewDrawer()"
    >
      <path
          d="M1 7H14.7143"
          stroke="gray"
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
      />
      <path
          d="M8.71436 1L14.7144 7L8.71436 13"
          stroke="gray"
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
      />
    </svg>
    <h2 class="text-2xl font-bold">Card</h2>
  </div>
</template>
