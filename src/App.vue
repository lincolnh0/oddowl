<script setup lang="ts">
import { RouterView } from "vue-router";
import NavBar from "@/components/NavBar.vue";
</script>

<template>
  <header>
    <NavBar />
  </header>
  <div class="container">
    <RouterView />
  </div>
</template>

<style lang="scss" scoped>
nav {
  width: 100%;
  text-align: center;
  margin-top: 2rem;
}
</style>
