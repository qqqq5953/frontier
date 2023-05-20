<script setup>
const props = defineProps({
  node: [Object, String],
});
</script>

<template lang="pug">
.ml-3(v-for="(value, key) in node", :key="key")
  template(v-if="typeof value === 'object'")
    details.pb-1.inline-block.cursor-pointer(open="")
      summary.font-bold.pb-1 {{ key }} :
      tree-item(:node="value")
  template(v-else="")
    .pb-1.cursor-auto
      span.font-bold {{ key }}
      |
      | :
      |
      span.text-red-400 {{ value }}
</template>

<style scoped>
details > summary {
  list-style: none;
}

summary::-webkit-details-marker {
  display: none;
}

summary::after {
  content: " [+]";
}
details[open] > summary:after {
  content: " [-]";
}
</style>