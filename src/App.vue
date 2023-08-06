<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <!-- <header>
    <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header>
  -->

  <RouterView />
</template>

<style>
:root {
  --board-size: 7;
  --tile-size: 64px;
}
body {
  background: lightpink;
}
</style>
