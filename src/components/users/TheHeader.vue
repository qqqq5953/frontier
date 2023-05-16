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

<template>
  <BaseHeader>
    <template #nav>
      <nav class="flex">
        <div class="space-x-3">
          <router-link :to="{ name: 'Home' }">ALL</router-link>
          <router-link :to="{ name: 'Favorite' }">Favorite</router-link>
        </div>
      </nav>
    </template>
    <template #optional>
      <div class="flex items-center gap-4">
        <select
          class="border border-slate-600 rounded"
          :value="perPage"
          @input="$emit('update:perPage', $event.target.value)"
        >
          <option :value="num" v-for="num in perPages" :key="num">
            {{ num }}
          </option>
        </select>
        <button
          :class="{ 'text-blue-500': currentMode === mode.name }"
          v-for="mode in modes"
          :key="mode.name"
          @click="setCurrentMode(mode.name)"
        >
          <i class="fa-solid" :class="mode.style"></i>
        </button>
      </div>
    </template>
  </BaseHeader>
</template>

