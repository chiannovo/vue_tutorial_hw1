<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const URLname = ref('');
const favoriteURL = ref('');
const favoritelist = ref([]);

onMounted(() => {
  const storage = localStorage.getItem('favorite-web');

  if (storage != null) {
    const result = JSON.parse(storage);
    favoritelist.value = result;
  }
});

const clearURL = () => {
  favoritelist.value = [];
  localStorage.removeItem('favorite-web');
  console.log('Delete!');
};

const addURL = () => {
  if (favoriteURL.value != '') {
    const newFavoritelist = {
      id: crypto.randomUUID(),
      name: favoriteURL.value,
      title: URLname.value,
    };
    favoritelist.value.unshift(newFavoritelist);

    localStorage.setItem('favorite-web', JSON.stringify(favoritelist.value));

    URLname.value = '';
    favoriteURL.value = '';
  }
};
const removeSingleURL = (id) => {
  const deleteItem = favoritelist.value.findIndex((n) => {
    return n.id == id;
  });
  console.log('將刪除 id:', id);
  favoritelist.value.splice(deleteItem, 1);
  console.log('刪除項目後清單內容:', favoritelist.value);
  localStorage.setItem('favorite-web', JSON.stringify(favoritelist.value));
};
</script>

<template>
  <div class="container mx-auto p-6">
    <div class="mb-6">
      <h1 class="text-3xl mb-4">
        收藏URL管理系統
        <div class="inline-flex gap-2 ml-4">
          <button @click="clearURL" class="btn btn-error btn-sm">
            全部清除
          </button>
        </div>
      </h1>
    </div>

    <!-- 新增URL -->
    <div class="mb-6">
      <div class="flex max-w-md mb-4">
        <input
          v-model.trim="URLname"
          type="text"
          placeholder="輸入網址標題(選填)"
          class="input input-bordered"
        />
      </div>
      <div class="flex gap-3">
        <input
          @keyup.enter="addURL"
          v-model.trim="favoriteURL"
          type="text"
          placeholder="請輸入網址(必填)"
          class="input input-bordered flex-1"
        />
        <button @click="addURL" class="btn btn-primary">新增</button>
      </div>
    </div>

    <!-- 分隔線 - 增加上下間距 -->
    <hr class="my-6" />

    <!-- 收藏列表 -->
    <div>
      <ul class="space-y-3">
        <li
          v-for="favoriteurl in favoritelist"
          :key="favoriteurl.id"
          class="p-3 bg-base-200 rounded-lg flex justify-between items-start"
        >
          <div class="flex-1">
            <div>標題: {{ favoriteurl.title || '無標題' }}</div>
            <div>
              URL:
              <a
                :href="favoriteurl.name"
                target="_blank"
                class="link link-primary"
                >{{ favoriteurl.name }}</a
              >
            </div>
          </div>
          <button
            @click="removeSingleURL(favoriteurl.id)"
            class="btn btn-sm btn-error ml-4"
          >
            刪除
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>
