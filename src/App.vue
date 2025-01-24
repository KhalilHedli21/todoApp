<script setup>
import { ref, computed, watch, onMounted } from 'vue';

// Reactive references
const todos = ref([]);
const name = ref('');
const inputContent = ref('');
const inputCategory = ref(null);

// Function to remove a todo
const removeTodo = (todoToRemove) => {
  todos.value = todos.value.filter(todo => todo !== todoToRemove);
};

// Computed property to sort todos in descending order by createdAt
const todosAsc = computed(() => {
  return [...todos.value].sort((a, b) => b.createdAt - a.createdAt);
});

// Function to add a new todo
const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value === null) {
    return;
  }
  
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: Date.now(),
  });

  // Clear input fields after adding
  inputContent.value = '';
  inputCategory.value = null;
};

// Watcher to save name to localStorage
watch(name, (newVal) => {
  localStorage.setItem('name', JSON.stringify(newVal));
});

// On component mount, retrieve name and todos from localStorage
onMounted(() => {
  name.value = JSON.parse(localStorage.getItem('name')) || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
<main class="app">
  <section class="greeting">
    <h2 class="title">
      Wassup, 
      <input type="text" placeholder="Name here" v-model="name" />
    </h2>
  </section>

  <section class="create-todo">
    <h3>Create a todo</h3>
    <form @submit.prevent="addTodo">
      <h4>What do you need to do?</h4>
      <input type="text" placeholder="Code using Vue" v-model="inputContent" />

      <h4>Pick a category</h4>
      <div class="options">
        <label>
          <input type="radio" name="category" value="Professional" v-model="inputCategory" />
          <span class="bubble business"></span>
          <div>Professional</div>
        </label>

        <label>
          <input type="radio" name="category" value="personal" v-model="inputCategory" />
          <span class="bubble personal"></span>
          <div>personal</div>
        </label>
      </div>

      <input type="submit" value="Add Todo" />
    </form>
  </section>

  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <div v-for="todo in todosAsc" :key="todo.createdAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content" />
        </div>

        <div class="actions"> 
          <button class="delete" @click="removeTodo(todo)">Remove</button>
        </div>
      </div>
    </div>
  </section>
</main>
</template>

<style scoped>
/* Add your styles here */
</style>
