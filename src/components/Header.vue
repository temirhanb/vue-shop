<script setup="">

  defineProps({
    totalPrice: Number
  });
  const emit = defineEmits(["viewDrawer"]);
</script>
<template>
  <header class="flex justify-between border-b border-slate-300 px-10 py-8">
    <div class="flex items-center gap-4">
      <img alt="logo" class="w-10" src="/logo.png">
      <div>
        <h2 class="text-xl font-bold uppercase">Vie sneakers</h2>
        <p class="text-slate-400">Magazine sport foot</p>
      </div>
    </div>
    <ul class="flex items-center gap-10">
      <li class="flex items-center cursor-pointer gap-3 text-slate-400 hover:text-black"
          @click="()=>emit('viewDrawer')">
        <img alt="cart" src="/cart.svg">
        <span>{{ totalPrice}} $.</span>
      </li>
      <li class="flex items-center cursor-pointer gap-3 text-slate-400 hover:text-black">
        <img alt="bookmarks" src="/heart.svg">
        <span>Bookmarks</span>
      </li>
      <li class="flex items-center cursor-pointer gap-3 text-slate-400 hover:text-black">
        <img alt="profile" src="/profile.svg">
        <span>Profile</span>
      </li>
    </ul>
  </header>
</template>