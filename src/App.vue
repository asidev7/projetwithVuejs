<template>
  <form action="" @submit.prevent="addTodo" class="p-3 bg-light shadow-sm rounded">
    <fieldset role="group" class="form-group">
      <div class="input-group">
        <input 
          type="text" 
          placeholder="Tâche à faire" 
          v-model="newtodo" 
          class="form-control" 
          aria-label="Nouvelle tâche"
        >
        <div class="input-group-append">
          <button 
            :disabled="newtodo.length == 0" 
            class="btn btn-primary" 
            type="submit"
          >
            Ajouter
          </button>
        </div>
      </div>
    </fieldset>
  </form>

  <div v-if="todos.length == 0" class="alert alert-info mt-3">
    Vous n'avez pas de tâches à faire :(
  </div>

  <div v-else class="mt-3">
    <ul class="list-group">
      <li 
        v-for="todo in sortedTodos()" 
        :key="todo.date" 
        :class="{completed: todo.completed}"
        class="list-group-item d-flex align-items-center"
      >
        <label class="form-check-label flex-grow-1">
          <input type="checkbox" class="form-check-input mr-2" v-model="todo.completed">
          {{ todo.title }}
        </label>
      </li>
    </ul>

    <label>


    <input type="checkbox" v-model="HiddeCompleted">
      Masquer les taches terminees
    </label>
  </div>
<checkbox :label="'bonjour'"/>
  
</template>

<script setup>
import checkbox from './checkbox.vue';
import {ref} from 'vue'
const newtodo = ref('')

const todos = ref([])

const HiddeCompleted  = ref(false)

const addTodo =()=>{
todos.value.push({
  title: newtodo.value,
  completed :false,
  date : Date.now()
})
newtodo.value =""
}

const sortedTodos = ()=>{
  const sortedTodos = todos.value.toSorted((a,b) => a.completed > b.completed ? 1:-1 )

  if (HiddeCompleted.value == true){
    return sortedTodos.filter(t=>t.completed == false)
  }
  return sortedTodos
}

</script>


<style>

.completed{
  opacity: 50%;
  text-decoration: line-through;
}
</style>