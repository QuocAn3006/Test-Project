<script setup>
import Editor from '@tinymce/tinymce-vue'
import { ref, onMounted, watch } from 'vue'
import { useToast } from 'vue-toastification'

const taskName = ref('')
const dueDate = ref('')
const tasks = ref([])
const toast = useToast()

const addTask = () => {
  const task = {
    id: Date.now(),
    name: taskName.value,
    dueDate: dueDate.value
  }

  tasks.value.push(task)
  saveTasksToLocalStorage()
  taskName.value = ''
  dueDate.value = ''
}
const checkTaskAlerts = () => {
  const alarm = new Date(dueDate.value)
  const currentTime = new Date()
  if (alarm > currentTime) {
    const timeDiff = alarm - currentTime
    setTimeout(() => {
      toast.success('task done')
    }, timeDiff)
  }
}
const saveTasksToLocalStorage = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

const removeTodo = (task) => {
  tasks.value = tasks.value.filter((t) => t !== task)
  saveTasksToLocalStorage()
}

const loadTasksFromLocalStorage = () => {
  const savedTasks = localStorage.getItem('tasks')
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks)
  }
}

onMounted(() => {
  loadTasksFromLocalStorage()
})

watch(dueDate, () => {
  checkTaskAlerts()
})
</script>

<template>
  <div class="main">
    <section class="create-todo">
      <h3 class="title">CREATE A TODO</h3>
      <form action="" @submit.prevent="addTask">
        <div class="form-input">
          <label for="" class="form-item">
            <span class="title-task">Task Decription</span>
            <Editor
              api-key="n56fr7jl6rzrnn3um7a00nf5kp6ga2mhfzpqwoqmhuzzavzu"
              :init="{ height: 200 }"
              v-model="taskName"
            ></Editor>
          </label>
          <div class="item-datetime">
            <label for="">
              <span>Due Date: </span>
              <input type="datetime-local" v-model="dueDate" />
            </label>
          </div>
          <button>Add</button>
        </div>
      </form>
    </section>

    <section class="todo-list">
      <h3 class="title-list">List to do</h3>
      <ul>
        <li v-for="task in tasks" :key="task.id" class="grid-container">
          <div v-html="task.name" class="grid-item"></div>
          <p class="grid-item">{{ new Date(task.dueDate).toLocaleString() }}</p>

          <i class="fa-solid fa-trash grid-item" @click="removeTodo(task)"></i>
        </li>
      </ul>
    </section>
  </div>
</template>

<style scoped>
.title {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 12px;
}

.form-item span {
  margin-bottom: 12px;
}
.form-input {
  display: flex;
  flex-direction: column;
}

.form-item {
  margin: 12px 0;
}

.form-input button {
  padding: 12px 20px;
  border-radius: 0.5rem;
  width: 30%;
  margin: 0 auto;
}

.form-input button:hover {
  background: #ccc;
}
.form-input input[type='text'] {
  display: block;
  width: 100%;
  font-size: 16px;
  padding: 8px 24px;
  color: var(--dark);
  background-color: #fff;
  border-radius: 0.5rem;
}
input[type='datetime-local'] {
  padding: 8px 16px;
  margin-bottom: 12px;
}
.item-datetime {
  display: flex;
  justify-content: space-around;
}

.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  column-gap: 6px;
  background-color: #fff;
  text-align: center;
}
.grid-item {
  padding: 20px;
  font-size: 16px;
  text-align: center;
}
li {
  list-style: none;
}

i {
  margin: 0 auto;
}

.title-list {
  margin: 12px 0;
}
</style>
