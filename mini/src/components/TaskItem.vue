<script setup>
// This component receives one task from App.vue
const props = defineProps({
  task: {
    type: Object,
    required: true
  }
})

// This component can send events back to App.vue
const emit = defineEmits(['toggle-task', 'delete-task'])

// Tell parent to toggle this task
function handleToggle() {
  emit('toggle-task', props.task.id)
}

// Tell parent to delete this task
function handleDelete() {
  emit('delete-task', props.task.id)
}
</script>

<template>
  <li class="task-item">
    <!-- If task.done is true, add done class -->
    <span :class="{ done: props.task.done }">
      {{ props.task.title }}
    </span>

    <div>
      <!-- Button text changes based on task status -->
      <button @click="handleToggle">
        {{ props.task.done ? 'Undo' : 'Done' }}
      </button>

      <button @click="handleDelete">
        Delete
      </button>
    </div>
  </li>
</template>

<style scoped>
.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;

  border: 1px solid #ddd;
  padding: 12px;
  margin-bottom: 8px;
  border-radius: 6px;
}

.done {
  text-decoration: line-through;
  color: gray;
}

button {
  padding: 10px 14px;
  cursor: pointer;
  margin-left: 6px;
}
</style>