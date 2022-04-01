<script setup lang="ts">
import { isDark, toggleDark } from '~/composables'
</script>

<template>
  <nav text-xl mt-6 mr-6 inline-flex gap-2 class="nav">
    <button class="icon-btn !outline-none" @click="toggleDark()">
      <div v-if="isDark" i-carbon-moon />
      <div v-else i-carbon-sun />
    </button>
  </nav>
</template>

<style>
.nav {
  position: absolute;
  top: 0;
  right: 0;
}
</style>
