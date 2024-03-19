<script setup>
  import {computed, provide, ref, watch} from "vue";
  import axios from "axios";

  import Header from "./components/Header.vue";
  import Drawer from "@/components/Drawer.vue";

  const carts = ref([]);

  const drawerIsOpen = ref(false);
  const isCreatingOrder = ref(false);

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
      :is-creating-order="isCreatingOrder"
      :sum-tax="sumTax"
      :total-price="sumTotalPrice"
      @create-order="createOrder"
  />
</template>

<style></style>
