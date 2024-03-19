<script setup>

  import {inject, onMounted, reactive, ref, watch} from "vue";
  import axios from "axios";
  import debounce from "lodash.debounce";

  import CardList from "@/components/CardList.vue";

  const items = ref([]);

  const filters = reactive({
    sortBy: "title",
    searchQuery: "",
  });

  const {carts, addToCart} = inject("drawer");
  const onChangeSelect = (event) => {
    filters.sortBy = event.target.value;
  };

  const onChangeSearch = debounce((event) => {
    filters.searchQuery = event.target.value;
  }, 300);

  const addToFavorite = async (item) => {
    try {
      if (!item.isFavorite) {
        item.isFavorite = true;

        const {data} = await axios.post("https://2934a1c29822d4b5.mokky.dev/favorites",
            {
              parentId: item.id,
              item
            }
        );
        item.favoriteId = data.id;
      } else {
        item.isFavorite = false;
        await axios.delete(`https://2934a1c29822d4b5.mokky.dev/favorites/${item.favoriteId}`);
        item.favoriteId = null;
      }
    } catch (err) {
      console.dir(err);
    }
  };

  const fetchFavorites = async () => {
    try {
      await axios.get("https://2934a1c29822d4b5.mokky.dev/favorites")
          .then(({data}) => items.value = items.value.map(item => {
            const favorite = data.find(favorite => favorite.parentId === item.id);
            if (!favorite) return item;
            return {
              ...item,
              isFavorite: true,
              favoriteId: favorite.id
            };
          }));

    } catch (err) {
      console.dir(err);
    }
  };

  const fetchItems = async () => {
    try {
      const params = {
        sortBy: filters.sortBy,
      };
      if (filters.searchQuery) {
        params.title = `*${filters.searchQuery}*`;
      }
      await axios.get("https://2934a1c29822d4b5.mokky.dev/items", {
        params
      }).then(res => items.value = res.data.map(item => ({
        ...item,
        isFavorites: false,
        favoriteId: null,
        isAdded: false
      })));
    } catch (err) {
      console.dir(err);
    }
  };

  onMounted(async () => {
    const localCart = localStorage.getItem("carts");
    carts.value = localCart ? JSON.parse(localCart) : [];
    await fetchItems();
    await fetchFavorites();
    items.value = items.value.map(item => ({...item, isAdded: carts.value.some(cartItem => cartItem.id === item.id)}));
  });

  watch(carts, () => {
    items.value = items.value.map(item => ({
      ...item,
      isAdded: false
    }));
  });

  watch(filters, fetchItems);

</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">All foots</h2>
    <div class="flex gap-4 flex-row">
      <select id="" class="py-2 px-3 border rounded-md outline-none" name="" @change="onChangeSelect">
        <option value="name">Name</option>
        <option value="price">Price: ASC</option>
        <option value="-price">Price: DESC</option>
      </select>
      <div class="relative">
        <img alt="search" class="absolute left-3 top-3" src="/search.svg">
        <input
            class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
            placeholder="Search..."
            type="text" @input="onChangeSearch"
        />
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList
        :items="items"
        @add-to-favorite="addToFavorite"
        @add-to-cart="addToCart"
    />
  </div>


</template>