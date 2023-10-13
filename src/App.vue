<script setup>
import Header from "./components/Header.vue";
import Weight from "./components/Weight.vue";

</script>

<template>
  <div class="app">
    <Header />
    <Weight />
  </div>
</template>

<style lang="scss" scoped>
@import "./assets/styles/main.scss";

.app {
  display: flex;
  align-items: center;
  flex-direction: column;
  background-color: $bg-color;
  height: 100%;
}
</style>
