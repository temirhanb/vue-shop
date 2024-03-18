<script setup>
  import {computed, onMounted, provide, reactive, ref, watch} from "vue";
  import axios from "axios";

  import Header from "./components/Header.vue";
  import CardList from "./components/CardList.vue";
  import Drawer from "@/components/Drawer.vue";

  const items = ref([]);
  const carts = ref([]);

  const drawerIsOpen = ref(false);
  const isCreatingOrder = ref(false);

  const filters = reactive({
    sortBy: "title",
    searchQuery: "",
  });

  const sumTotalPrice = computed(() => carts.value.reduce((previousValue, currentValue) => previousValue + currentValue.price, 0));
  const sumTax = computed(() => sumTotalPrice.value * 0.05);

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

  const addToFavorite = async (item) => {
    try {
      if (!item.isFavorite) {
        item.isFavorite = true;

        const {data} = await axios.post("https://2934a1c29822d4b5.mokky.dev/favorites",
            {
              parentId: item.id,
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

  const addToCart = (item) => {
    if (!item.isAdded) {
      item.isAdded = true;
      carts.value.push(item);
    } else {
      item.isAdded = false;
      carts.value.splice(carts.value.indexOf(item), 1);
    }
  };

  const createOrder = async () => {
    try {
      isCreatingOrder.value = true;
      const {data} = await axios.post(`https://2934a1c29822d4b5.mokky.dev/orders`, {
        items: carts.value,
        totalPrice: sumTotalPrice.value
      });

      carts.value = [];
      return data;
    } catch (err) {
      console.dir(err);
    } finally {
      isCreatingOrder.value = false;
    }
  };

  const viewDrawer = () => {
    drawerIsOpen.value = !drawerIsOpen.value;
  };

  const onChangeSelect = (event) => {
    filters.sortBy = event.target.value;
  };

  const onChangeSearch = (event) => {
    filters.searchQuery = event.target.value;
  };

  onMounted(async () => {
    const localCart = localStorage.getItem("carts");
    carts.value = localCart ? JSON.parse(localCart) : [];
    await fetchItems();
    await fetchFavorites();
    items.value = items.value.map(item => ({...item, isAdded: carts.value.some(cartItem => cartItem.id === item.id)}));
  });

  watch(filters, fetchItems);
  watch(carts, () => {
    items.value = items.value.map(item => ({
      ...item,
      isAdded: false
    }));
  });

  watch(carts, () => {
    localStorage.setItem("carts", JSON.stringify(carts.value));
  }, {deep: true});
  provide("drawer", {viewDrawer, carts, addToCart});
</script>

<template>

  <div class='w-4/5 m-auto bg-white rounded-xl shadow-xl mt-14'>

    <Header
        :total-price="sumTotalPrice"
        @view-drawer="viewDrawer"
    />

    <div class="p-10">
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

      <CardList
          :items="items"
          @add-to-favorite="addToFavorite"
          @add-to-cart="addToCart"
      />

    </div>
  </div>

  <Drawer
      v-if="drawerIsOpen"
      :is-creating-order="isCreatingOrder"
      :sum-tax="sumTax"
      :total-price="sumTotalPrice"
      @create-order="createOrder"
  />
</template>

<style></style>
