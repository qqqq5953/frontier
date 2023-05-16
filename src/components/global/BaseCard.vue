<script setup>
const props = defineProps({
  user: Object,
});
</script>

<template>
  <div class="w-1/5">
    <div class="border border-slate-300 rounded p-4 m-4">
      <img :src="user.img" alt="" />
      <p class="text-xs truncate">{{ user.name }}</p>
    </div>
  </div>
</template>