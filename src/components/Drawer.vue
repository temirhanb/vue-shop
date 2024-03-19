<script setup>
  import axios from "axios";
  import {inject, ref} from "vue";
  import DrawerHead from "./DrawerHead.vue";
  import CartListItem from "./CartListItem.vue";
  import InfoBlock from "@/components/InfoBlock.vue";

  const props = defineProps({
    sumTax: Number,
    totalPrice: Number,
  });

  const isCreatingOrder = ref(false);
  const orderId = ref(null);

  const {viewDrawer, carts} = inject("drawer");

  const createOrder = async () => {
    try {
      isCreatingOrder.value = true;
      const {data} = await axios.post(`https://2934a1c29822d4b5.mokky.dev/orders`, {
        items: carts.value,
        totalPrice: props.totalPrice
      });

      carts.value = [];
      orderId.value = data.id;
      return data;
    } catch (err) {
      console.dir(err);
    } finally {
      isCreatingOrder.value = false;
    }
  };

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70" @click="()=>viewDrawer()"/>
  <div class="bg-white w-96 h-full fixed right-0 top-0 p-8 z-20">
    <DrawerHead/>
    <CartListItem/>

    <div
        v-if="totalPrice===0 ||orderId"
        class="flex h-full items-center"
    >
      <InfoBlock
          v-if="!totalPrice && !orderId"
          description="Please, add more items in cart"
          image-url="/package-icon.png"
          title="Cart is empty"
      />
      <InfoBlock
          v-if="orderId"
          :description="`Order No. ${orderId} has been shipped from the warehouse!`"
          image-url="/order-success-icon.png"
          title="Prepared!"
      />
    </div>

    <div v-if="totalPrice" class="flex flex-col gap-4 my-6">
      <div class="flex gap-2">
        <span>Full price: </span>
        <div class="flex-1 border-b border-dashed"></div>
        <strong>{{ totalPrice }} $.</strong>
      </div>
      <div class="flex gap-2">
        <span>Tax 5%: </span>
        <div class="flex-1 border-b border-dashed"></div>
        <strong>{{ sumTax }} $.</strong>
      </div>
      <button
          :disabled="!totalPrice"
          class="bg-lime-500 transition disabled:bg-slate-400 rounded-xl w-full py-3 text-white hover:bg-lime-600 active:bg-lime-700"
          @click="createOrder"
      >
        {{ isCreatingOrder ? "Prepared!!" : "Make an order!" }}
      </button>
    </div>
  </div>
</template>
