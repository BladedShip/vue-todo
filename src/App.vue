<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const content = ref("");
const category = ref(null);

const todoAsc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

const addTodo = () => {
  if (content.value.trim() === "" || category.value === null) {
    return;
  }
  todos.value.push({
    content: content.value,
    category: category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  content.value='';
  category.value=null;
};
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Sup,
        <input type="text" placeholder="Your Name" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g finish chores" v-model="content" />
        <h4>Pick a Category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="work"
              v-model="category"
            />
            <span class="bubble business"></span>
            <div>Work</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div
          v-for="todo in todoAsc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
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
  </main>
</template>
