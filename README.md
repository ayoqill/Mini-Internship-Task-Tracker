# Mini-Internship-Task-Tracker

learn Vue3 Vite project

This project is a small Vue + Vite app built before learning Nuxt.

The purpose is to understand Vue basics first:

- Store and update data
- Display data on the screen
- Use input and buttons
- Show list of tasks
- Split UI into components
- Use props and emits
- Save data using localStorage
- Fetch fake API data

| Feature                    | Status |
| -------------------------- | ------ |
| Add task                   | Done   |
| Delete task                | Done   |
| Mark task as done          | Done   |
| Undo completed task        | Done   |
| Filter all tasks           | Done   |
| Filter completed tasks     | Done   |
| Filter pending tasks       | Done   |
| Task counter               | Done   |
| Use components             | Done   |
| Use props                  | Done   |
| Use emits                  | Done   |
| Save tasks in localStorage | Done   |
| Load fake API tasks        | Done   |

# Main Vue Concepts Learned

1. ref()

ref() is used to store data that can change.

Example:

const newTask = ref('')
const tasks = ref([])

In <script setup>, use .value:

newTask.value = ''
tasks.value.push(newTask.value)

In <template>, no need .value:

{{ newTask }}


2. v-model

v-model connects an input field with Vue data.

Example:

<input v-model="newTask" placeholder="Enter new task" />

Meaning:

When user types in input, newTask changes automatically.


3. @click

@click runs a function when a button is clicked.

Example:

<button @click="addTask">
  Add Task
</button>

Meaning:

When user clicks the button, run addTask().


4. v-if and v-else

Used to show something based on a condition.

Example:

<p v-if="filteredTasks.length === 0">
  No task found.
</p>

<ul v-else>
  ...
</ul>

Meaning:

If no task exists, show message.
If tasks exist, show task list.


5. v-for

Used to loop through a list.

Example:

<TaskItem
  v-for="task in filteredTasks"
  :key="task.id"
  :task="task"
/>

Meaning:

For every task in filteredTasks, create one TaskItem component.


6. computed

computed is used to calculate a value automatically.

Example:

const filteredTasks = computed(() => {
  if (filter.value === 'completed') {
    return tasks.value.filter(task => task.done)
  }

  if (filter.value === 'pending') {
    return tasks.value.filter(task => !task.done)
  }

  return tasks.value
})

Meaning:

filteredTasks changes automatically when tasks or filter changes.


7. Props

Props are used when parent component sends data to child component.

Example in App.vue:

<TaskItem :task="task" />

Example in TaskItem.vue:

const props = defineProps({
  task: {
    type: Object,
    required: true
  }
})

Meaning:

App.vue sends task data to TaskItem.vue.


8. Emits

Emits are used when child component sends an action/event back to parent component.

Example in TaskItem.vue:

const emit = defineEmits(['toggle-task', 'delete-task'])

function handleDelete() {
  emit('delete-task', props.task.id)
}

Example in App.vue:

<TaskItem
  @delete-task="deleteTask"
  @toggle-task="toggleTask"
/>

Meaning:

TaskItem.vue tells App.vue what action happened.
App.vue handles the real logic.


9. onMounted()

onMounted() runs when the component first opens.

Used for loading saved tasks from localStorage.

Example:

onMounted(() => {
  const savedTasks = localStorage.getItem('tasks')

  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks)
  }
})

Meaning:

When app opens, get saved tasks from browser storage.


10. watch()

watch() detects changes in data.

Used to save tasks automatically when tasks change.

Example:

watch(
  tasks,
  (updatedTasks) => {
    localStorage.setItem('tasks', JSON.stringify(updatedTasks))
  },
  { deep: true }
)

Meaning:

Every time tasks change, save the latest tasks.


11. localStorage

localStorage saves data in the browser.

Save data:

localStorage.setItem('tasks', JSON.stringify(tasks.value))

Get data:

const savedTasks = localStorage.getItem('tasks')

Convert array/object to text:

JSON.stringify(tasks.value)

Convert text back to array/object:

JSON.parse(savedTasks)


12. Fetch API

Used to get fake task data from online API.

Example:

async function loadFakeTasks() {
  loadingApi.value = true

  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=5')
    const data = await response.json()

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

Meaning:

fetch() gets data from API.
await waits for the data.
map() changes API data into our task format.
try/catch handles error.
Component Roles
App.vue

Main parent component.

It stores:

newTask
tasks
filter
loadingApi

It controls:

addTask()
deleteTask()
toggleTask()
changeFilter()
loadFakeTasks()

It also sends data to child components.

TaskItem.vue

Child component for one task.

It receives task data using props.

It sends actions back using emits:

toggle-task
delete-task
TaskFilter.vue

Child component for filter buttons and task counter.

It receives:

currentFilter
total
completed
pending

It sends action back using emits:

change-filter
Important Logic Summary
Add Task
function addTask() {
  if (newTask.value.trim() === '') return

  tasks.value.push({
    id: Date.now(),
    title: newTask.value,
    done: false
  })

  newTask.value = ''
}

Meaning:

If input is not empty, add a new task and clear the input.
Delete Task
function deleteTask(taskId) {
  tasks.value = tasks.value.filter(task => task.id !== taskId)
}

Meaning:

Keep all tasks except the selected task.
Done / Undo Task
function toggleTask(taskId) {
  const task = tasks.value.find(task => task.id === taskId)

  if (task) {
    task.done = !task.done
  }
}

Meaning:

If task is not done, make it done.
If task is done, make it not done.
Filter Tasks
const filteredTasks = computed(() => {
  if (filter.value === 'completed') {
    return tasks.value.filter(task => task.done)
  }

  if (filter.value === 'pending') {
    return tasks.value.filter(task => !task.done)
  }

  return tasks.value
})

Meaning:

All = show all tasks
Completed = show only done tasks
Pending = show only not done tasks
Parent and Child Concept

Very important Vue idea:

Parent gives data to child = props
Child sends action to parent = emits

Example:

App.vue gives task to TaskItem.vue
TaskItem.vue sends delete-task event back to App.vue
App.vue deletes the task