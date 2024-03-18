<script setup>
  import Card from "./Card.vue";

  defineProps({
    items: Array,
  });

  const emit = defineEmits([
    "addToFavorite",
    "addToCart",
  ]);
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
        v-for="item in items"
        :id="item.id"
        :key="item.id"
        :image-url="item.imageUrl"
        :is-added="item.isAdded"
        :is-favorite="item.isFavorite"
        :on-click-added="()=>emit('addToCart',item)"
        :on-click-favorite="()=>emit('addToFavorite',item)"
        :price="item.price"
        :title="item.title"
    />
  </div>
</template>
