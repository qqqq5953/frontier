<script setup>
import { onBeforeUnmount, ref, watch, watchEffect } from "vue";
import axios from "axios";
import TheCard from "@/components/users/TheCard.vue";
import TheList from "@/components/users/TheList.vue";
import TheHeader from "@/components/users/TheHeader.vue";
import BaseModal from "@/components/global/BaseModal.vue";
import BaseContainer from "@/components/global/BaseContainer.vue";

// modal
const isModalOpen = ref(false);
function toggleModal(isOpen) {
  isModalOpen.value = isOpen;
}
function showDetail(e) {
  const nodeName = e.target.nodeName;
  const modes = {
    card: () => {
      if (nodeName !== "UL" && nodeName !== "LI") toggleModal(true);
    },
    list: () => {
      if (nodeName !== "UL") toggleModal(true);
    },
  };
  const show = modes[currentMode.value];
  show();
}

// users
const memo = ref(new Map());
const users = ref(null);
async function getUsers(results = 10, page = 1) {
  const key = `${results}_${page}`;
  if (memo.value.get(key)) return memo.value.get(key);

  const res = await axios.get(
    `https://randomuser.me/api/?inc=picture,name,id&seed=91885730dab261f5&page=${page}&results=${results}`
  );

  const users = res.data.results.map((user) => {
    const { picture, name, id } = user;
    return {
      id: id.value,
      img: picture.large,
      name: name.first + " " + name.last,
    };
  });

  memo.value.set(key, users);
  console.log(res);

  return users;
}

const selectedUser = ref(null);
function selectUser(user) {
  selectedUser.value = user;
}

// page and mode
const currentMode = ref("card");
const modes = ref([
  { name: "card", style: "fa-table-cells-large" },
  { name: "list", style: "fa-list" },
]);

function setCurrentMode(mode) {
  currentMode.value = mode;
}

const perPages = ref([10, 30, 50]);
const perPage = ref(10);
const currentPage = ref(1);

function getPageStatus() {
  const pageStatus = localStorage.getItem("pageStatus");
  if (pageStatus) {
    const { memoCurrentPage, memoPerPage, memoCurrentMode } =
      JSON.parse(pageStatus);
    perPage.value = memoPerPage;
    currentMode.value = memoCurrentMode;
    currentPage.value = memoCurrentPage;
  }
}

getPageStatus();

function storePageStatus({ currentPage, perPage, currentMode }) {
  localStorage.setItem(
    "pageStatus",
    JSON.stringify({
      memoCurrentMode: currentMode,
      memoCurrentPage: currentPage,
      memoPerPage: perPage,
    })
  );
}

watch(
  [perPage, currentPage, currentMode],
  async ([newPerPage, newCurrentPage, newCurrentMode]) => {
    users.value = await getUsers(newPerPage, newCurrentPage);

    storePageStatus({
      currentMode: newCurrentMode,
      currentPage: newCurrentPage,
      perPage: newPerPage,
    });
  },
  { immediate: true }
);

onBeforeUnmount(() => {
  storePageStatus({
    currentMode: currentMode.value,
    currentPage: currentPage.value,
    perPage: perPage.value,
  });
});
</script>

<template>
  <Teleport to="body">
    <BaseModal v-show="isModalOpen" @toggleModal="toggleModal">
      <img :src="selectedUser?.img" alt="" />
      <p class="text-xs truncate">{{ selectedUser?.name }}</p>
    </BaseModal>
  </Teleport>
  <BaseContainer>
    <template #header>
      <TheHeader
        :perPages="perPages"
        :modes="modes"
        :currentMode="currentMode"
        v-model:perPage="perPage"
        @setCurrentMode="setCurrentMode"
      />
    </template>
    <template #main>
      <template v-if="currentMode === 'card'">
        <ul class="flex flex-wrap -mx-4" @click="showDetail($event)">
          <li class="w-1/5" v-for="user in users" :key="user.id">
            <TheCard :user="user" @click="selectUser(user)" />
          </li>
        </ul>
      </template>
      <template v-if="currentMode === 'list'">
        <ul class="flex flex-col gap-y-4 my-4" @click="showDetail($event)">
          <li v-for="user in users" :key="user.id">
            <TheList :user="user" @click="selectUser(user)" />
          </li>
        </ul>
      </template>
    </template>
  </BaseContainer>
</template>


