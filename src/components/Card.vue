<script setup>

  defineProps({
    imageUrl: String,
    title: String,
    price: Number,
    id: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickAdded: Function,
    onClickFavorite: Function,
  });

</script>

<template>
  <div
      class="relative border border-slate-100 rounded-3xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition bg-white"
  >
    <img
        v-if="onClickFavorite !==null"
        :src="isFavorite ? '/like-2.svg' : '/like-1.svg'"
        alt="likes"
        @click="onClickFavorite"
    />

    <img :src="imageUrl" alt="sneaker"/>
    <p class="mt-2">Man foot {{ title }}</p>
    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <p class="text-slate-400">Price:</p>
        <strong>{{ price }} $.</strong>
      </div>
      <img
          v-if="onClickAdded !== null"
          :src="isAdded ? '/checked.svg' : '/plus.svg'"
          alt="plus"
          @click="onClickAdded"
      />
    </div>
  </div>
</template>
