<template>
  <div class="container mt-5">
    <form @submit.prevent="addTodo" class="p-4 bg-white shadow rounded todo-form">
      <fieldset role="group" class="form-group">
        <div class="input-group">
          <input 
            type="text" 
            placeholder="Tâche à faire" 
            v-model="newTodo" 
            class="form-control" 
            aria-label="Nouvelle tâche"
          >
          <div class="input-group-append">
            <button 
              :disabled="newTodo.length === 0" 
              class="btn btn-primary" 
              type="submit"
            >
              Ajouter
            </button>
          </div>
        </div>
      </fieldset>
    </form>

    <div v-if="todos.length === 0" class="alert alert-info mt-3 text-center">
      Vous n'avez pas de tâches à faire :(
    </div>

    <div v-else class="mt-4">
      <ul class="list-group">
        <li 
          v-for="todo in sortedTodos()" 
          :key="todo.date" 
          :class="{'completed': todo.completed}"
          class="list-group-item d-flex align-items-center justify-content-between"
        >
          <div class="d-flex align-items-center">
            <input 
              type="checkbox" 
              class="form-check-input mr-2" 
              v-model="todo.completed"
            >
            <span class="todo-title">{{ todo.title }}</span>
          </div>
          <span class="text-muted">{{ formatDate(todo.date) }}</span>
        </li>
      </ul>

      <div class="form-check mt-3">
        <input 
          type="checkbox" 
          class="form-check-input" 
          v-model="hideCompleted"
        >
        <label class="form-check-label">
          Masquer les tâches terminées
        </label>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';

// State variables
const newTodo = ref('');
const todos = ref([]);
const hideCompleted = ref(false);

// Add a new task
const addTodo = () => {
  todos.value.push({
    title: newTodo.value,
    completed: false,
    date: Date.now(),
  });
  newTodo.value = '';
  saveTodos(); // Save to local storage
};

// Sort and filter tasks
const sortedTodos = () => {
  let sorted = [...todos.value].sort((a, b) => a.completed - b.completed);
  if (hideCompleted.value) {
    sorted = sorted.filter((todo) => !todo.completed);
  }
  return sorted;
};

// Save to local storage
const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value));
};

// Load from local storage
const loadTodos = () => {
  const savedTodos = localStorage.getItem('todos');
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos);
  }
};

// Format date for display
const formatDate = (timestamp) => {
  const date = new Date(timestamp);
  return date.toLocaleDateString() + ' ' + date.toLocaleTimeString();
};

// Load todos on mounted
onMounted(loadTodos);

// Watch changes in todos and save automatically
watch(todos, saveTodos, { deep: true });
</script>

<style scoped>
/* Styles for the to-do form */
.todo-form {
  max-width: 600px;
  margin: 0 auto;
}

.form-control {
  height: 45px;
  font-size: 16px;
}

.btn-primary {
  background-color: #007bff;
  border-color: #007bff;
  height: 45px;
}

.list-group-item {
  background-color: #f8f9fa;
  border-color: #e9ecef;
  padding: 15px 20px;
}

.completed .todo-title {
  text-decoration: line-through;
  color: #6c757d;
}

.completed {
  opacity: 0.6;
}

.todo-title {
  font-size: 16px;
  font-weight: 500;
}

input[type='checkbox'] {
  transform: scale(1.2);
}

.alert {
  font-size: 18px;
  font-weight: 500;
}

.form-check-label {
  font-size: 16px;
  margin-left: 8px;
}
</style>
