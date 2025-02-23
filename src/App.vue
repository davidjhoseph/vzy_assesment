<script setup>
import Poll from './components/Poll.vue'
</script>

<template>
  <div class="app-container">
    <Poll />
  </div>
</template>

<style>
.app-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}
</style>
