<script setup>
import BaseHeader from "@/components/global/BaseHeader.vue";

const props = defineProps({
  perPages: Array,
  perPage: Number,
  modes: Array,
  currentMode: String,
});
const emit = defineEmits(["update:perPage"]);

function setCurrentMode(mode) {
  emit("setCurrentMode", mode);
}
</script>

<template lang="pug">
base-header
  template(#nav="")
    nav.flex
      .space-x-3
        router-link(:to="{ name: 'Home' }") ALL
        router-link(:to="{ name: 'Favorite' }") Favorite
  template(#optional="")
    .flex.items-center.gap-4
      select.border.border-slate-600.rounded(
        :value="perPage",
        @input="$emit('update:perPage', parseInt($event.target.value))"
      )
        option(:value="num", v-for="num in perPages", :key="num")
          | {{ num }}
      button(
        :class="{ 'text-blue-500': currentMode === mode.name }",
        v-for="mode in modes",
        :key="mode.name",
        @click="setCurrentMode(mode.name)"
      )
        i.fa-solid(:class="mode.style")
</template>