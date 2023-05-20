<script setup>
const emit = defineEmits(["toggleModal"]);

function toggleModal(isOpen) {
  emit("toggleModal", isOpen);
}
</script>

<template lang="pug">
.fixed.inset-0.grid.place-items-center(
  class="bg-slate-600/60",
  @click.self="toggleModal(false)"
)
  .relative.bg-white.rounded.p-12
    button.absolute.right-4.top-4.rounded-full.w-7.h-7.text-blue-500.font-bold(
      class="bg-slate-200/50 hover:bg-slate-100/70 hover:text-blue-400",
      @click="toggleModal(false)"
    )
      | X
    slot
</template>