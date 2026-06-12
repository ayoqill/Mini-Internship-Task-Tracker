<script setup>
import { ref } from 'vue'

const message = ref('Hello Vue')
</script>

<template>
  <main>
    <h1>{{ message }}</h1>
  </main>
</template>

<style scoped>
main {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 50px;
}
</style>