<script setup>
  import DrawerHead from "./DrawerHead.vue";
  import CartListItem from "./CartListItem.vue";
  import {inject} from "vue";
  import InfoBlock from "@/components/InfoBlock.vue";

  defineProps({
    sumTax: Number,
    totalPrice: Number,
    isCreatingOrder: Boolean,
  });

  const emit = defineEmits(["createOrder"]);
  const {viewDrawer} = inject("drawer");

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70" @click="()=>viewDrawer()"/>
  <div class="bg-white w-96 h-full fixed right-0 top-0 p-8 z-20">
    <DrawerHead/>
    <CartListItem/>
    <div
        v-if="totalPrice===0"
        class="flex h-full items-center"
    >
      <InfoBlock
          description="Please, add more items in cart"
          image-url="/package-icon.png"
          title="Cart is empty"
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
          @click="()=>emit('createOrder')"
      >
        {{ isCreatingOrder ? "Prepared!!" : "Make an order!" }}
      </button>
    </div>
  </div>
</template>
