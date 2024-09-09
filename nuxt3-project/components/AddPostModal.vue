<!-- components/AddPostModal.vue -->
<template>
  <div v-if="show" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
    <div class="bg-white p-4 rounded-lg shadow-lg">
      <h2 class="text-xl mb-4">Add New Post</h2>
      <form @submit.prevent="addPost">
        <div class="mb-4">
          <label class="block text-gray-700">Title</label>
          <input v-model="title" type="text" class="w-full px-3 py-2 border rounded" required />
        </div>
        <div class="mb-4">
          <label class="block text-gray-700">Body</label>
          <textarea v-model="body" class="w-full px-3 py-2 border rounded" required></textarea>
        </div>
        <div class="flex justify-end">
          <button type="button" @click="closeModal" class="px-4 py-2 bg-gray-500 text-white rounded mr-2">Cancel</button>
          <button type="submit" class="px-4 py-2 bg-teal-500 text-white rounded">Add Post</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import { usePostStore } from '~/store/postStore';

const props = defineProps({
  show: Boolean,
});

const emit = defineEmits(['close']);

const title = ref('');
const body = ref('');

const postStore = usePostStore();

const closeModal = () => {
  emit('close');
};

const addPost = async () => {
  await postStore.addPost({ title: title.value, body: body.value });
  closeModal();
};

watch(() => props.show, (newVal) => {
  if (!newVal) {
    title.value = '';
    body.value = '';
  }
});
</script>
