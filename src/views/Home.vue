<script setup>
import { computed, onBeforeUnmount, ref, watch, watchEffect } from "vue";
import axios from "axios";
import TheCard from "@/components/users/TheCard.vue";
import TheList from "@/components/users/TheList.vue";
import TheHeader from "@/components/users/TheHeader.vue";
import BaseModal from "@/components/global/BaseModal.vue";
import BaseContainer from "@/components/global/BaseContainer.vue";
import BasePagination from "@/components/global/BasePagination.vue";

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

// memo
const memo = ref(new Map());
function getMemo(pageId) {
  return memo.value.get(pageId);
}
function setMemo(pageId, users) {
  memo.value.set(pageId, users);
}

// users
const users = ref([]);
const totalUsers = ref(150);
async function getUsers(perPage = 10, currentPage = 1, isLastPage) {
  const pageId = `${perPage}_${currentPage}`;
  if (memo.value.has(pageId)) return getMemo(pageId);

  const res = await axios.get(
    `https://randomuser.me/api/?inc=picture,name,id&nat=us&seed=91885730dab261f5&page=${currentPage}&results=${perPage}`
  );

  const lastPageItems = isLastPage ? totalUsers.value % perPage : 0;
  const endItemIndex =
    lastPageItems === 0 ? res.data.results.length : lastPageItems;

  const users = res.data.results
    .map((user) => {
      const { picture, name, id } = user;
      return {
        id: id.value,
        img: picture.large,
        name: name.first + " " + name.last,
      };
    })
    .slice(0, endItemIndex);

  setMemo(pageId, users);
  return users;
}

const selectedUser = ref(null);
function selectUser(user) {
  selectedUser.value = user;
}

// mode
const currentMode = ref("card");
const modes = ref([
  { name: "card", style: "fa-table-cells-large" },
  { name: "list", style: "fa-list" },
]);

function setCurrentMode(mode) {
  currentMode.value = mode;
}

// page
const perPages = ref([10, 30, 50]);
const perPage = ref(10);
const currentPage = ref(1);
const maxBtn = ref(5);

const totalPages = computed(() => {
  const pages = [];
  const lastPage = Math.ceil(totalUsers.value / perPage.value);
  const isLessThanFive = lastPage <= maxBtn.value;
  const isFirstFive =
    currentPage.value >= 1 && currentPage.value <= maxBtn.value;
  const isLastFive =
    currentPage.value > lastPage - maxBtn.value &&
    currentPage.value <= lastPage;

  if (isLessThanFive) {
    for (let num = 1; num <= lastPage; num++) {
      pages.push(num);
    }
  } else if (isFirstFive) {
    for (let num = 1; num <= maxBtn.value; num++) {
      pages.push(num);
    }
    pages.push("...");
    pages.push(lastPage);
  } else if (isLastFive) {
    for (let num = lastPage - maxBtn.value; num <= lastPage; num++) {
      pages.push(num);
    }
  } else {
    for (let num = currentPage.value - 2; num <= currentPage.value + 2; num++) {
      pages.push(num);
    }
    pages.push("...");
    pages.push(lastPage);
  }

  return pages;
});

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

function setPage(page) {
  currentPage.value = page;
}

watch(
  [perPage, currentPage, currentMode],
  async ([newPerPage, newCurrentPage, newCurrentMode]) => {
    // 如果 currentPage 在最後一頁，perPage 由小切換到大時，currentPage 要調整到新的排版的最後一頁
    const lastPage = totalPages.value[totalPages.value.length - 1];
    const adjustedCurrentPage =
      lastPage < newCurrentPage ? lastPage : newCurrentPage;
    const isLastPage = adjustedCurrentPage === lastPage;

    currentPage.value = adjustedCurrentPage;
    users.value = await getUsers(newPerPage, currentPage.value, isLastPage);

    storePageStatus({
      currentMode: newCurrentMode,
      perPage: newPerPage,
      currentPage: currentPage.value,
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
getFavItemsMap();

function getFavItemsMap() {
  const favoriteItems = JSON.parse(localStorage.getItem("favoriteItems"));
  if (!favoriteItems) return;

  Object.entries(favoriteItems).forEach(([key, value]) => {
    favoriteMap.value.set(key, value);
  });
}

function storeFavItems(favItems) {
  try {
    localStorage.setItem("favoriteItems", favItems);
  } catch (error) {
    console.log("favoriteItems localStorage is full", error);
  }
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

  storeFavItems(savedItems);
  favoriteMap.value.set(id, { name, img });
}

function removeFavorite(id) {
  if (!favoriteMap.value.has(id)) return;
  const favoriteItems = localStorage.getItem("favoriteItems");

  if (!favoriteItems) return;
  const { [id]: removedItem, ...remainingItems } = JSON.parse(favoriteItems);

  storeFavItems(JSON.stringify(remainingItems));
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
      <main class="flex flex-col p-4 border grow h-full pb-20">
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
    <template #footer>
      <BasePagination
        class="fixed bottom-0"
        :totalPages="totalPages"
        :currentPage="currentPage"
        :maxBtn="maxBtn"
        @setPage="setPage"
      />
    </template>
  </BaseContainer>
</template>