<script setup>
import BaseCard from "@/components/global/BaseCard.vue";

const props = defineProps({
  user: Object,
});
</script>

<template lang="pug">
base-card
  .relative.grid.place-items-center.rounded.text-slate-200(
    style="aspect-ratio: 1/1"
  )
    i.fa-solid.fa-user.fa-2xl
    img.absolute.w-full.rounded(:src="user.img", :alt="user.name")
  p.text-xs.truncate.pt-3(class="w-4/5")
    | {{ user.name }}
</template>