<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header class="fixed bg-white w-full h-20">
    <img alt="Gannesoft" class="logo" src="@/assets/logo.svg" height="125" />

  </header>
  <div class="flex pt-20 h-screen overflow-hidden">

    <nav>
      <RouterLink class="navLink" to="">Home</RouterLink>
      <RouterLink class="navLink" to="/about">About</RouterLink>
      <RouterLink class="navLink" to="/loan">Loan EMI Calculator</RouterLink>
    </nav>
    <RouterView class="h-auto overflow-auto" />
  </div>

</template>

<style scoped>

.navLink {
  @apply p-2 block bg-green-300;
}
.navLink:hover {
  @apply bg-yellow-200
}
.navLink.router-link-active {
  @apply bg-green-400
}

</style>