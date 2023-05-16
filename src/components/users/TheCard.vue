<script setup>
import BaseCard from "@/components/global/BaseCard.vue";

const props = defineProps({
  user: Object,
});
</script>

<template>
  <BaseCard>
    <img :src="user.img" alt="" />
    <p class="text-xs truncate">{{ user.name }}</p>
  </BaseCard>
</template>