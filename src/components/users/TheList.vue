<script setup>
import BaseList from "@/components/global/BaseList.vue";
const props = defineProps({
  user: Object,
});
</script>

<template>
  <BaseList>
    <img :src="user.img" alt="" />
    <p class="text-xs truncate">{{ user.name }}</p>
  </BaseList>
</template>