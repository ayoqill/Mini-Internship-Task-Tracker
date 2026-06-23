<script setup>
// Receive current filter from App.vue
defineProps({
  currentFilter: {
    type: String,
    required: true
  }
})

// Send event back to App.vue
const emit = defineEmits(['change-filter'])

// Send selected filter to parent
function setFilter(filterName) {
  emit('change-filter', filterName)
}
</script>

<template>
  <div class="filter-row">
    <button
      :class="{ active: currentFilter === 'all' }"
      @click="setFilter('all')"
    >
      All
    </button>

    <button
      :class="{ active: currentFilter === 'completed' }"
      @click="setFilter('completed')"
    >
      Completed
    </button>

    <button
      :class="{ active: currentFilter === 'pending' }"
      @click="setFilter('pending')"
    >
      Pending
    </button>
  </div>
</template>

<style scoped>
.filter-row {
  display: flex;
  justify-content: center;
  gap: 8px;
  margin-bottom: 20px;
}

button {
  padding: 10px 14px;
  cursor: pointer;
}

.active {
  font-weight: bold;
  border: 2px solid black;
}
</style>