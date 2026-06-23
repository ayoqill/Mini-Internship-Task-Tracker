<script setup>
// Import Vue features
import { ref, computed, onMounted, watch } from 'vue'

// Import child component
import TaskItem from './components/TaskItem.vue'
import TaskFilter from './components/TaskFilter.vue'

// Store input text
const newTask = ref('')

// Store all tasks
const tasks = ref([])

// Store current selected filter
const filter = ref('all')

// Load saved tasks when app opens
onMounted(() => {
  const savedTasks = localStorage.getItem('tasks')

  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks)
  }
})

// Save tasks whenever tasks change
watch(
  tasks,
  (updatedTasks) => {
    localStorage.setItem('tasks', JSON.stringify(updatedTasks))
  },
  { deep: true }
)

// Add new task
function addTask() {
  if (newTask.value.trim() === '') return

  tasks.value.push({
    id: Date.now(),
    title: newTask.value,
    done: false
  })

  newTask.value = ''
}

// Delete task by id
function deleteTask(taskId) {
  tasks.value = tasks.value.filter(task => task.id !== taskId)
}

// Mark task as done/undo
function toggleTask(taskId) {
  const task = tasks.value.find(task => task.id === taskId)

  if (task) {
    task.done = !task.done
  }
}

// Change selected filter
function changeFilter(selectedFilter) {
  filter.value = selectedFilter
}

// Return tasks based on selected filter
const filteredTasks = computed(() => {
  if (filter.value === 'completed') {
    return tasks.value.filter(task => task.done)
  }

  if (filter.value === 'pending') {
    return tasks.value.filter(task => !task.done)
  }

  return tasks.value
})
</script>

<template>
  <main class="container">
    <h1>Mini Internship Task Tracker</h1>

    <!-- Input and button -->
    <div class="input-row">
      <input
        v-model="newTask"
        placeholder="Enter new task"
        @keyup.enter="addTask"
      />

      <button @click="addTask">
        Add Task
      </button>
    </div>

    <!-- Filter buttons -->
    <TaskFilter
      :current-filter="filter"
      @change-filter="changeFilter"
    />

    <!-- Show if no task -->
    <p v-if="filteredTasks.length === 0" class="empty">
      No task found.
    </p>

    <!-- Task list -->
    <ul v-else class="task-list">
      <!-- 
        props:
        :task="task" sends task data to TaskItem

        emits:
        @toggle-task receives event from TaskItem
        @delete-task receives event from TaskItem
      -->
      <TaskItem
        v-for="task in filteredTasks"
        :key="task.id"
        :task="task"
        @toggle-task="toggleTask"
        @delete-task="deleteTask"
      />
    </ul>
  </main>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  font-size: 32px;
  line-height: 1.2;
}

.input-row {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 10px;
}

button {
  padding: 10px 14px;
  cursor: pointer;
  margin-left: 6px;
}

.empty {
  text-align: center;
  color: gray;
}

.task-list {
  list-style: none;
  padding: 0;
}
</style>