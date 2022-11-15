<script setup lang="ts">

import three3d from './components/three3d.vue'
</script>

<template>
  <div class="main">
    <three3d></three3d>
  </div>
</template>

<style>
*,
html,
body {
  margin: 0;
  padding: 0;
}

.main {
  width: 100vw;
  height: 100vh;
  border: 1px solid #ddd;
  box-sizing: border-box;
}
</style>
