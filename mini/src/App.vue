<script setup>
// Import Vue features
import { ref, computed, onMounted, watch } from 'vue'

// Import components
import TaskItem from './components/TaskItem.vue'
import TaskFilter from './components/TaskFilter.vue'

// Store input text
const newTask = ref('')

// Store all tasks
const tasks = ref([])

// Store current selected filter
const filter = ref('all')

// Store API loading status
const loadingApi = ref(false)

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

// Load fake tasks from API
async function loadFakeTasks() {
  loadingApi.value = true

  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=5')
    const data = await response.json()

    // Convert API data into our task format
    tasks.value = data.map(item => ({
      id: item.id,
      title: item.title,
      done: item.completed
    }))
  } catch (error) {
    alert('Failed to load fake tasks')
    console.log(error)
  } finally {
    loadingApi.value = false
  }
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

// Count completed tasks
const completedCount = computed(() => {
  return tasks.value.filter(task => task.done).length
})

// Count pending tasks
const pendingCount = computed(() => {
  return tasks.value.filter(task => !task.done).length
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

    <!-- Fake API button -->
    <button class="api-btn" @click="loadFakeTasks">
      {{ loadingApi ? 'Loading...' : 'Load Fake API Tasks' }}
    </button>

    <!-- Filter component -->
    <TaskFilter
      :current-filter="filter"
      :total="tasks.length"
      :completed="completedCount"
      :pending="pendingCount"
      @change-filter="changeFilter"
    />

    <!-- Show if no task -->
    <p v-if="filteredTasks.length === 0" class="empty">
      No task found.
    </p>

    <!-- Task list -->
    <ul v-else class="task-list">
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
  margin-bottom: 12px;
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

.api-btn {
  width: 100%;
  margin-bottom: 20px;
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