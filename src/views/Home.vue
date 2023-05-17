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
  const omitNodes_card = ["UL", "LI", "I"];
  const omitNodes_list = ["UL", "I"];
  const modes = {
    card: () => {
      if (omitNodes_card.indexOf(nodeName) === -1) toggleModal(true);
    },
    list: () => {
      if (omitNodes_list.indexOf(nodeName) === -1) toggleModal(true);
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
  if (memo.value.has(key)) return memo.value.get(key);

  const res = await axios.get(
    `https://randomuser.me/api/?inc=picture,name,id&nat=us&seed=91885730dab261f5&page=${page}&results=${results}`
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
getPageStatus();

function getPageStatus() {
  const pageStatus = localStorage.getItem("all_pageStatus");
  if (pageStatus) {
    const { memoCurrentPage, memoPerPage, memoCurrentMode } =
      JSON.parse(pageStatus);
    perPage.value = memoPerPage;
    currentMode.value = memoCurrentMode;
    currentPage.value = memoCurrentPage;
  }
}

function storePageStatus({ currentPage, perPage, currentMode }) {
  localStorage.setItem(
    "all_pageStatus",
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

// favoorite
const favoriteMap = ref(new Map());
checkFavorite();

function checkFavorite() {
  const favoriteItems = JSON.parse(localStorage.getItem("favoriteItems"));
  if (!favoriteItems) return;

  Object.entries(favoriteItems).forEach(([key, value]) => {
    favoriteMap.value.set(key, value);
  });
}

function addFavorite(user) {
  const { id, img, name } = user;
  if (favoriteMap.value.has(id)) return;

  const favoriteItems = localStorage.getItem("favoriteItems");
  const savedItems = favoriteItems
    ? JSON.stringify({
        ...JSON.parse(favoriteItems),
        [id]: { name, img },
      })
    : JSON.stringify({
        [id]: { name, img },
      });

  localStorage.setItem("favoriteItems", savedItems);
  favoriteMap.value.set(id, { name, img });
}

function removeFavorite(id) {
  if (!favoriteMap.value.has(id)) return;
  const favoriteItems = localStorage.getItem("favoriteItems");

  if (!favoriteItems) return;
  const { [id]: removedItem, ...remainingItems } = JSON.parse(favoriteItems);

  localStorage.setItem("favoriteItems", JSON.stringify(remainingItems));
  favoriteMap.value.delete(id);
}
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
      <main class="p-4">
        <ul
          class="flex flex-wrap"
          :class="currentMode === 'card' ? '-mx-4' : 'flex-col gap-y-4 my-3'"
          @click="showDetail($event)"
        >
          <li
            class="relative"
            :class="currentMode === 'card' ? 'w-1/5' : 'w-full'"
            v-for="user in users"
            :key="user.id"
          >
            <TheCard
              :user="user"
              @click="selectUser(user)"
              v-if="currentMode === 'card'"
            />
            <TheList
              :user="user"
              @click="selectUser(user)"
              v-else-if="currentMode === 'list'"
            />
            <i
              class="fa-regular fa-heart absolute bottom-4 right-4 cursor-pointer"
              v-if="!favoriteMap.has(user.id)"
              @click="addFavorite(user)"
            ></i>
            <i
              class="fa-solid fa-heart absolute bottom-4 right-4 cursor-pointer"
              v-else
              @click="removeFavorite(user.id)"
            ></i>
          </li>
        </ul>
      </main>
    </template>
  </BaseContainer>
</template>


