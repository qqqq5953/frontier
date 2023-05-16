<script setup>
import { ref, watchEffect } from "vue";
import axios from "axios";
import BaseCard from "@/components/global/BaseCard.vue";
import BaseList from "@/components/global/BaseList.vue";
import BaseContainer from "@/components/global/BaseContainer.vue";
import BaseHeader from "@/components/global/BaseHeader.vue";

const props = defineProps({
  perPage: Number,
  mode: String,
});

const perPages = ref([10, 30, 50]);
const perPage = ref(10);
const currentMode = ref("card");
const modes = ref([
  { name: "card", style: "fa-table-cells-large" },
  { name: "list", style: "fa-list" },
]);

function setCurrentMode(mode) {
  currentMode.value = mode;
}

const users = ref(null);
async function getUsers(results = 10, page = 1) {
  const res = await axios.get(
    `https://randomuser.me/api/?inc=picture,name,id&seed=91885730dab261f5&page=${page}&results=${results}`
  );

  console.log(res);

  return res.data.results.map((user) => {
    const { picture, name, id } = user;
    return {
      id: id.value,
      img: picture.large,
      name: name.first + " " + name.last,
    };
  });
}

watchEffect(async () => {
  users.value = await getUsers(perPage.value);
});
</script>

<template>
  <BaseContainer>
    <template #header>
      <BaseHeader
        :perPages="perPages"
        :modes="modes"
        :currentMode="currentMode"
        v-model:perPage="perPage"
        @setCurrentMode="setCurrentMode"
      />
    </template>
    <template #main>
      <div class="flex flex-wrap -mx-4">
        <template v-if="currentMode === 'card'">
          <BaseCard v-for="user in users" :key="user.id" :user="user" />
        </template>
      </div>
      <div class="flex flex-col gap-y-4 my-4">
        <template v-if="currentMode === 'list'">
          <BaseList v-for="user in users" :key="user.id" :user="user" />
        </template>
      </div>
    </template>
  </BaseContainer>
</template>


