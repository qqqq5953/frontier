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

<template>
  <div class="flex justify-center min-h-screen">
    <section class="space-y-2 w-1/2 bg-gray-300/70 p-4">
      <div class="text-right">
        <button
          class="bg-slate-500 hover:bg-slate-400 text-white rounded px-2 py-1"
          @click="addNode"
        >
          + Add New Pair
        </button>
      </div>
      <div v-for="(node, index) in nodes" :key="index">
        <div class="flex flex-wrap gap-2 font-bold">
          <input
            class="rounded px-2 py-1 flex-1"
            v-model="node.name"
            placeholder="Key"
            @input="updateTree"
          />
          <input
            class="rounded px-2 py-1 flex-1"
            v-model="node.value"
            placeholder="Value"
            @input="updateTree"
          />
          <button
            class="bg-slate-400/40 hover:bg-slate-400/30 text-slate-500 rounded px-2 py-1 w-7"
            @click="deleteNode(index)"
          >
            -
          </button>
        </div>
      </div>
    </section>

    <section class="w-1/2 bg-gray-200 p-4">
      <TreeItem :node="tree" />
    </section>
  </div>
</template>

