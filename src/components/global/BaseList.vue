<script setup>
const props = defineProps({
  user: Object,
});
</script>

<template>
  <div class="flex gap-x-4 border border-slate-300 rounded p-4">
    <img :src="user.img" alt="" />
    <p class="text-xs truncate">{{ user.name }}</p>
  </div>
</template>