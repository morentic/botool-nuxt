<script setup lang="ts">
import 'material-symbols';
useHead({
  htmlAttrs: {
    'data-theme': 'dracula',
  },
});
</script>

<template>
  <div class="bg-base">
    <NuxtLoadingIndicator />
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
