<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref ('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' ||  input_category.value === null) {
    return
  }
  
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}

const removeTodo = todo => {
    todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

/* This part of the code is using the onMounted lifecycle hook from the Vue Composition API. It's a function that gets executed when the component is mounted in the DOM. Inside the callback function, it retrieves the value associated with the key 'name' from the browser's localStorage using the localStorage.getItem() function.*/ 
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || [] )
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3 class="create-line">CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
          <input 
            type="text" 
            placeholder="e.g. make a video" 
            v-model="input_content" />

        <h4>Pick a category</h4>
          <div class="options">
            <label>
              <input 
                type="radio" 
                name="category" 
                id="category"
                value="business"
                v-model="input_category"  />
              <span class="bubble business"></span>
              <div>Business</div>
            </label>

            <label>
              <input 
                type="radio" 
                name="category" 
                id="category"
                value="personal"
                v-model="input_category"  />
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>
          <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-lis">
      <h3>TODO LIST</h3>
      <div class="list">
          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label> 

            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
      </div>
    </section>

    {{ input_category }}   
  </main>
</template>

