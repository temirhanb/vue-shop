<script setup>
  import {inject} from "vue";
  import CartItem from "./CartItem.vue";

  const {carts, addToCart} = inject("drawer");
  const clickAddToCart = (cart) => {
    addToCart(cart);
  };
  console.log(carts);
</script>


<template>
  <div v-auto-animate class="flex flex-col gap-5">
    <cart-item
        v-for="cart in carts"
        :id="cart.id"
        :key="cart.id"
        :image-url="cart.imageUrl"
        :on-click-added="()=>clickAddToCart(cart)"
        :price="cart.price"
        :title="cart.title"
    />
  </div>
</template>

