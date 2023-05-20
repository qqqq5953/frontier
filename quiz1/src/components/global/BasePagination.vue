<script setup>
import { computed, ref, watch } from "vue";
const props = defineProps({
  totalPages: Array,
  maxBtn: Number,
  currentPage: Number,
});

const emit = defineEmits(["setPage"]);

function toPage(page) {
  if (page === "...") return;
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
</script>

<template lang="pug">
nav.flex.flex-wrap.justify-center.items-center.gap-x-4.w-full.p-4.backdrop-blur-md(
  class="bg-white/30"
)
  button.bg-white.border.border-blue-400.text-blue-400.rounded.shadow-md.w-10.h-8(
    class="disabled:bg-blue-200 disabled:text-gray-100 disabled:border-transparent disabled:cursor-not-allowed hover:bg-blue-400 hover:text-white",
    @click="toPrev()",
    :disabled="currentPage === 1"
  )
    i.fa-solid.fa-chevron-left

  template(v-for="page in totalPages", :key="page")
    template(v-if="page !== '...'")
      button.rounded.w-10.h-8.shadow-md(
        class="hover:bg-blue-400 hover:text-white",
        :class="currentPage === page ? 'bg-blue-400 text-white' : 'border border-blue-400 text-blue-400 bg-white'",
        @click="toPage(page)"
      )
        | {{ page }}
    template(v-else="")
      .text-blue-400
        | {{ page }}

  button.bg-white.border.border-blue-400.text-blue-400.rounded.shadow-md.w-10.h-8(
    class="disabled:bg-blue-200 disabled:text-gray-100 disabled:border-transparent disabled:cursor-not-allowed hover:bg-blue-400 hover:text-white",
    @click="toNext()",
    :disabled="currentPage === totalPages[totalPages.length - 1]"
  )
    i.fa-solid.fa-chevron-right

  button.relative.h-8.px-6.border.border-slate-500.bg-white.rounded.text-center.group(
    v-if="totalPages.length > 5"
  )
    div {{ currentPage }}
    .absolute.left-0.bottom-full.w-full.overflow-hidden.mb-2.border.border-slate-500.bg-white.shadow-md.rounded.hidden(
      class="group-focus-within:block"
    )
      ul.py-2.space-y-1.overflow-auto.max-h-80
        li(
          class="hover:bg-blue-400 hover:text-white",
          v-for="page in totalPages[totalPages.length - 1]",
          :key="page"
        )
          button.w-full(@click="toPage(page)") {{ page }}
</template>

