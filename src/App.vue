<template>
  <div>
    <header>
      <h1>Todo List</h1>
    </header>
    <section class="container">
      <div class="form-container">
        <form class="todo-form" @submit.prevent="addNewTodo">
          <input type="text" class="todo-input" v-model.trim="newTitle" />
          <button class="add-todo" type="submit">
            <i class="fas fa-plus-circle"></i>
          </button>
        </form>
        <div class="select">
          <select class="filter-todos" name="todos" v-model="filterValue">
            <option value="all">All</option>
            <option value="completed">Completed</option>
            <option value="uncompleted">Uncompleted</option>
          </select>
        </div>
      </div>
      <div class="todo-container">
        <ul class="todolist">
          <li class="todo" :class="{ 'todo--completed': todo.isCompleted }" v-for="todo in filteredTodos" :key="todo.id">
            <p class="todo__title" :class="{ completed: todo.isCompleted }">{{ todo.title }}</p>
            <span class="todo__createdAt">{{ formatDate(todo.createdAt) }}</span>
            <button class="todo__check" type="button" @click="toggleTodo(todo.id)"><i class="far fa-check-square" @click.stop="toggleTodo(todo.id)"></i></button>
            <button class="todo__remove" type="button" @click="removeTodo(todo.id)"><i class="far fa-trash-alt" @click.stop="removeTodo(todo.id)"></i></button>
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const newTitle = ref('')
const filterValue = ref('all')
const todos = ref([])

const loadTodos = () => {
  try {
    const saved = JSON.parse(localStorage.getItem('todos')) || []
    todos.value = Array.isArray(saved) ? saved : []
  } catch (e) {
    todos.value = []
  }
}

const persistTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

onMounted(loadTodos)
watch(todos, persistTodos, { deep: true })

const addNewTodo = () => {
  if (!newTitle.value) return
  const todo = {
    id: Date.now(),
    createdAt: new Date().toISOString(),
    title: newTitle.value,
    isCompleted: false,
  }
  todos.value = [...todos.value, todo]
  newTitle.value = ''
}

const removeTodo = (id) => {
  todos.value = todos.value.filter((t) => t.id !== id)
}

const toggleTodo = (id) => {
  todos.value = todos.value.map((t) => (t.id === id ? { ...t, isCompleted: !t.isCompleted } : t))
}

const filteredTodos = computed(() => {
  if (filterValue.value === 'completed') return todos.value.filter((t) => t.isCompleted)
  if (filterValue.value === 'uncompleted') return todos.value.filter((t) => !t.isCompleted)
  return todos.value
})

const formatDate = (iso) => {
  try {
    return new Date(iso).toLocaleDateString('fa-IR')
  } catch {
    return ''
  }
}
</script> 