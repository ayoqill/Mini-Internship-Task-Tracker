<script setup>
// Receive data from App.vue
defineProps({
  currentFilter: {
    type: String,
    required: true
  },

  total: {
    type: Number,
    required: true
  },

  completed: {
    type: Number,
    required: true
  },

  pending: {
    type: Number,
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
  <div class="filter-box">
    <!-- Task counter -->
    <p>
      Total: {{ total }} |
      Completed: {{ completed }} |
      Pending: {{ pending }}
    </p>

    <!-- Filter buttons -->
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
  </div>
</template>

<style scoped>
.filter-box {
  text-align: center;
  margin-bottom: 20px;
}

.filter-row {
  display: flex;
  justify-content: center;
  gap: 8px;
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