<script setup>
import BaseCard from "@/components/global/BaseCard.vue";

const props = defineProps({
  user: Object,
});
</script>

<template>
  <BaseCard>
    <img :src="user.img" :alt="user.name" class="rounded" />
    <p class="text-xs truncate w-4/5 pt-3">
      {{ user.name }}
    </p>
  </BaseCard>
</template>