<template>
  <div class="throbber">
    <div class="spinner"></div>
  </div>
</template>

<script setup>
const loading = ref(false);
</script>

<style scoped>
.throbber {
  @apply flex justify-center items-center h-full;
}
.spinner {
  @apply border-4 border-gray-200 border-t-gray-800 rounded-full w-9 h-9 animate-spin;
}
</style>
