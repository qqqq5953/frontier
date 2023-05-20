<script setup>
import BaseList from "@/components/global/BaseList.vue";
const props = defineProps({
  user: Object,
});
</script>

<template lang="pug">
base-list
  .relative.grid.place-items-center.rounded.text-slate-200(
    class="w-1/4",
    style="aspect-ratio: 1/1"
  )
    i.fa-solid.fa-user.fa-2xl
    img.absolute.w-full.rounded(:src="user.img", :alt="user.name")
  p.text-xs.truncate {{ user.name }}
</template>