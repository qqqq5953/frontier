<script setup>
const props = defineProps({
  node: [Object, String],
});
</script>
<template>
  <div class="ml-3" v-for="(value, key) in node" :key="key">
    <template v-if="typeof value === 'object'">
      <details class="pb-1 inline-block cursor-pointer" open>
        <summary class="font-bold pb-1">{{ key }} :</summary>
        <TreeItem :node="value" />
      </details>
    </template>
    <template v-else>
      <div class="pb-1 cursor-auto">
        <span class="font-bold">{{ key }}</span> :
        <span class="text-red-400">{{ value }}</span>
      </div>
    </template>
  </div>
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