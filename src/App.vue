<script setup>
  import {computed, provide, ref, watch} from "vue";
  import axios from "axios";

  import Header from "./components/Header.vue";
  import Drawer from "@/components/Drawer.vue";

  const carts = ref([]);

  const drawerIsOpen = ref(false);

  const sumTotalPrice = computed(() => carts.value.reduce((previousValue, currentValue) => previousValue + currentValue.price, 0));
  const sumTax = computed(() => sumTotalPrice.value * 0.05);

  const addToCart = (item) => {
    if (!item.isAdded) {
      item.isAdded = true;
      carts.value.push(item);
    } else {
      item.isAdded = false;
      carts.value.splice(carts.value.indexOf(item), 1);
    }
  };

  const viewDrawer = () => {
    drawerIsOpen.value = !drawerIsOpen.value;
  };

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
      <router-view></router-view>
    </div>
  </div>

  <Drawer
      v-if="drawerIsOpen"
      :sum-tax="sumTax"
      :total-price="sumTotalPrice"
  />
</template>

<style></style>
