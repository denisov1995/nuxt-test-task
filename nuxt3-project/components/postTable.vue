<template>
  <div class="container mx-auto p-4">
    <Throbber v-if="postStore.loading" />
    <div v-if="error" class="text-red-500">{{ error }}</div>
    <table
      v-else
      class="min-w-full divide-y divide-gray-200 shadow-md rounded-lg"
    >
      <thead class="bg-teal-500 text-white">
        <tr>
          <th
            @click="sortTable('id')"
            class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider cursor-pointer"
          >
            ID
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider"
          >
            Title
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider"
          >
            Body
          </th>
        </tr>
      </thead>
      <tbody class="bg-white divide-y divide-gray-200">
        <tr v-for="post in sortedItems" :key="post.id">
          <td class="px-6 py-4">{{ post.id }}</td>
          <td class="px-6 py-4">{{ post.title }}</td>
          <td class="px-6 py-4">{{ post.body }}</td>
        </tr>
      </tbody>
    </table>
    <div class="mt-4 flex justify-center">
      <button
        @click="prevPage"
        :disabled="page === 1"
        class="px-4 py-2 mx-1 bg-teal-500 text-white rounded disabled:opacity-50"
      >
        Previous
      </button>
      <button
        @click="nextPage"
        :disabled="page === totalPages"
        class="px-4 py-2 mx-1 bg-teal-500 text-white rounded disabled:opacity-50"
      >
        Next
      </button>
    </div>
    <div class="mt-4 flex justify-center">
      <button
        @click="showModal = true"
        class="px-4 py-2 bg-teal-500 text-white rounded"
      >
        Add New Post
      </button>
    </div>
    <AddPostModal :show="showModal" @close="showModal = false" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { usePostStore } from "~/store/postStore";
import Throbber from "~/components/Throbber";
import AddPostModal from "~/components/AddPostModal";

const postStore = usePostStore();
const posts = ref([]);
const page = ref(1);
const itemsPerPage = 10;
const sortKey = ref("id");
const sortOrder = ref("asc");
const showModal = ref(false);
const newArr = ref([]);
const error = ref("");

const route = useRoute();
const router = useRouter();

const loadPosts = async () => {
  await postStore.fetchPosts(page.value);
  posts.value = postStore.posts;
  newArr.value = postStore.addedArray;

  if (page.value > totalPages.value) {
    error.value = "Страница не существует. Перенаправление на последнюю доступную страницу.";
    page.value = totalPages.value;
    updateQueryParams();
    loadPosts();
  } else if (page.value < 1) {
    error.value = "Страница не существует. Перенаправление на первую страницу.";
    page.value = 1;
    updateQueryParams();
    loadPosts();
  } else {
    error.value = "";
  }
};

const totalPages = computed(() =>
  Math.ceil((postStore.totalPosts + newArr.value.length) / itemsPerPage)
);

const nextPage = () => {
  if (page.value < totalPages.value) {
    page.value++;
    updateQueryParams();
    loadPosts();
  } else {
    error.value = "Страница не существует. Перенаправление на последнюю доступную страницу.";
    page.value = totalPages.value;
    updateQueryParams();
    loadPosts();
  }
};

const prevPage = () => {
  if (page.value > 1) {
    page.value--;
    updateQueryParams();
    loadPosts();
  } else {
    error.value = "Страница не существует. Перенаправление на первую страницу.";
    page.value = 1;
    updateQueryParams();
    loadPosts();
  }
};

const updateQueryParams = () => {
  router.push({
    path: route.path,
    query: { ...route.query, page: page.value }
  });
};

const sortedItems = computed(() => {
  return posts.value.sort((a, b) => {
    if (sortOrder.value === "asc") {
      return a[sortKey.value] - b[sortKey.value];
    } else {
      return b[sortKey.value] - a[sortKey.value];
    }
  });
});

const sortTable = (key) => {
  if (sortKey.value === key) {
    sortOrder.value = sortOrder.value === "asc" ? "desc" : "asc";
  } else {
    sortKey.value = key;
    sortOrder.value = "asc";
  }
};

onMounted(() => {
  if (route.query.page) {
    page.value = parseInt(route.query.page);
  }
  loadPosts();
});
</script>
