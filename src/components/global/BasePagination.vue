<script setup>
import { computed, ref, watch } from "vue";
const props = defineProps({
  totalPages: Array,
  maxBtn: Number,
  currentPage: Number,
});

const emit = defineEmits(["setPage"]);

function toPage(page) {
  emit("setPage", page);
}

function toPrev() {
  let currentPage = props.currentPage;
  if (currentPage === 1) return;
  currentPage--;
  emit("setPage", currentPage);
}

function toNext() {
  let currentPage = props.currentPage;
  if (currentPage === props.totalPages[props.totalPages.length]) return;
  currentPage++;
  emit("setPage", currentPage);
}

const jumpToPage = ref(props.currentPage);

function jumpTo() {
  emit("setPage", jumpToPage.value);
}

watch(
  () => props.currentPage,
  (newVal) => {
    jumpToPage.value = newVal;
  }
);
</script>

<template>
  <nav
    class="flex flex-wrap justify-center items-center gap-x-4 w-full p-4 bg-white/30 backdrop-blur-md"
  >
    <button
      class="bg-white border border-blue-400 text-blue-400 rounded shadow-md w-10 h-8 disabled:bg-blue-200 disabled:text-gray-100 disabled:border-transparent disabled:cursor-not-allowed hover:bg-blue-400 hover:text-white"
      @click="toPrev()"
      :disabled="currentPage === 1"
    >
      <i class="fa-solid fa-chevron-left"></i>
    </button>

    <button
      class="rounded w-10 h-8 shadow-md hover:bg-blue-400 hover:text-white"
      :class="
        currentPage === page
          ? 'bg-blue-400 text-white'
          : 'border border-blue-400 text-blue-400 bg-white'
      "
      v-for="page in totalPages"
      :key="page"
      @click="toPage(page)"
    >
      {{ page }}
    </button>

    <button
      class="bg-white border border-blue-400 text-blue-400 rounded shadow-md w-10 h-8 disabled:bg-blue-200 disabled:text-gray-100 disabled:border-transparent disabled:cursor-not-allowed hover:bg-blue-400 hover:text-white"
      @click="toNext()"
      :disabled="currentPage === totalPages[totalPages.length - 1]"
    >
      <i class="fa-solid fa-chevron-right"></i>
    </button>

    <select
      class="absolute right-4 h-8 w-12 border border-slate-500 rounded"
      @change="jumpTo"
      v-model="jumpToPage"
    >
      <option
        :value="page"
        v-for="page in totalPages[totalPages.length - 1]"
        :key="page"
      >
        {{ page }}
      </option>
    </select>
  </nav>
</template>