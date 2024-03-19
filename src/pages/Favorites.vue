<script setup>
  import axios from "axios";
  import {onMounted, ref} from "vue";
  import CardList from "@/components/CardList.vue";

  const favorites = ref([]);

  onMounted(async () => {
    try {
      const {data} = await axios.get("https://2934a1c29822d4b5.mokky.dev/favorites");
      favorites.value = data.map(el => el.item);
    } catch (err) {
      console.dir(err);
    }
  });
</script>


<template>
    <h2 class="text-3xl font-bold mb-8">Bookmarks</h2>
    <CardList :items="favorites" :is-favorites="true"/>
</template>