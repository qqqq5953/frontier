<script setup>
import { computed, ref } from "vue";
import TreeItem from "./TreeItem.vue";

const props = defineProps({
  nodes: Array,
});

const nodes = ref(
  props.nodes || [
    { name: "fruit.apple.red", value: "sweet" },
    { name: "fruit.banana.yellow", value: "soft" },
    { name: "vegi.broccoli", value: "good" },
  ]
);

const addNode = () => {
  nodes.value.push({ name: "", value: "" });
};

const deleteNode = (index) => {
  nodes.value.splice(index, 1);
};

const buildTree = (nodes) => {
  const tree = {};

  nodes.forEach((node) => {
    const names = node.name.split(".");
    if (names[names.length - 1] === "") return;
    let currNode = tree;

    names.forEach((name, index) => {
      if (!currNode[name]) {
        if (index === names.length - 1) {
          currNode[name] = node.value;
        } else {
          currNode[name] = {};
        }
      }

      currNode = currNode[name];
    });
  });

  return tree;
};

const tree = computed(() => {
  return buildTree(nodes.value);
});
</script>

<template lang="pug">
.flex.justify-center.min-h-screen
  section.space-y-2.p-4(class="w-1/2 bg-gray-300/70")
    .text-right
      button.bg-slate-500.text-white.rounded.px-2.py-1(
        class="hover:bg-slate-400",
        @click="addNode"
      )
        | + Add New Pair
    div(v-for="(node, index) in nodes", :key="index")
      .flex.flex-wrap.gap-2.font-bold
        input.rounded.px-2.py-1.flex-1(
          v-model="node.name",
          placeholder="Key",
          @input="updateTree"
        )
        input.rounded.px-2.py-1.flex-1(
          v-model="node.value",
          placeholder="Value",
          @input="updateTree"
        )
        button.text-slate-500.rounded.px-2.py-1.w-7(
          class="bg-slate-400/40 hover:bg-slate-400/30",
          @click="deleteNode(index)"
        )
          | -
  section.bg-gray-200.p-4(class="w-1/2")
    tree-item(:node="tree")
</template>

