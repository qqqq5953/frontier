<script setup>
const emit = defineEmits(["toggleModal"]);

function toggleModal(isOpen) {
  emit("toggleModal", isOpen);
}
</script>

<template>
  <div
    class="fixed inset-0 grid place-items-center bg-slate-600/60"
    @click.self="toggleModal(false)"
  >
    <div class="relative bg-white rounded p-12">
      <button
        class="absolute right-4 top-4 rounded-full w-7 h-7 text-blue-500 font-bold bg-slate-200/50 hover:bg-slate-100/70 hover:text-blue-400"
        @click="toggleModal(false)"
      >
        X
      </button>
      <slot></slot>
    </div>
  </div>
</template>