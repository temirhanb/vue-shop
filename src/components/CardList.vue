<script setup>
  import Card from "./Card.vue";
  import InfoBlock from "@/components/InfoBlock.vue";

  defineProps({
    items: Array,
    isFavorites: Boolean
  });

  const emit = defineEmits([
    "addToFavorite",
    "addToCart",
  ]);
</script>

<template>
  <div v-auto-animate class="grid grid-cols-4 gap-5">
    <Card
        v-for="item in items"
        :id="item.id"
        :key="item.id"
        :image-url="item.imageUrl"
        :is-added="item.isAdded"
        :is-favorite="item.isFavorite"
        :on-click-added="isFavorites?null:()=>emit('addToCart',item)"
        :on-click-favorite="isFavorites?null:()=>emit('addToFavorite',item)"
        :price="item.price"
        :title="item.title"
    />
  </div>
</template>
