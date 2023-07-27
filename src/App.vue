<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = "null";
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

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a todo</h3>

      <form @submit.prevent="addTodo">
        <h4>whats on your todo list?</h4>
        <input
          type="text"
          placeholder="EX: Create a todo App"
          v-model="input_content"
        />

        <h4>Category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <div class="list">
        <div
          v-for="todo in todos_asc"
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

<style lang="scss">
input:not([type="radio"]):not([type="checkbox"]),
button {
  appearance: none;
  border: none;
  outline: none;
  background: none;
  cursor: initial;
}

body {
  background: $light;
  color: $dark;

  section {
    margin-top: 2rem;
    margin-bottom: 2rem;
    padding-left: 1.5rem;
    padding-right: 1.5em;
  }

  h3 {
    color: $dark;
    font-size: 1rem;
    font-weight: 400;
    margin-bottom: 0.5rem;
  }

  h4 {
    color: $grey;
    font-size: 0.875rem;
    font-weight: 700;
    margin-bottom: 0.5rem;
  }
}

.greeting {
  display: flex;

  .title input {
    margin-left: 0.5rem;
    flex: 1 1 0%;
    min-width: 0;
  }

  input {
    color: $dark;
    font-size: 1.5rem;
    font-weight: 700;
  }
}

.create-todo {
  input[type="text"] {
    display: block;
    width: 100%;
    font-size: 1.125rem;
    padding: 1rem 1.5rem;
    color: $dark;
    background-color: #fff;
    border-radius: 0.5rem;
    box-shadow: $shadow;
    margin-bottom: 1.5rem;
  }
  .options {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-gap: 1rem;
    margin-bottom: 1.5rem;
  }

  label {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 1.5rem;
    background-color: #fff;
    border-radius: 0.5rem;
    box-shadow: $shadow;
    cursor: pointer;
  }
}

input[type="radio"],
input[type="checkbox"] {
  display: none;
}

.bubble {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid $personal;
  box-shadow: $business-glow;

  &:after {
    content: "";
    display: block;
    opacity: 0;
    width: 0px;
    height: 0px;
    background-color: $business;
    box-shadow: $business-glow;
    border-radius: 50%;
    transition: 0.2s ease-in-out;
  }
}

.business {
  border-color: $business;
  box-shadow: $business-glow;

  .personal {
    border-color: $personal;
    box-shadow: $personal-glow;

    &:after {
      background-color: $personal;
      box-shadow: $personal-glow;
    }
  }
}

input:checked ~ .bubble::after {
  width: 10px;
  height: 10px;
  opacity: 1;
}

.create-todo .options label div {
  color: $dark;
  font-size: 1.125rem;
  margin-top: 1rem;
}

.create-todo input[type="submit"] {
  display: block;
  width: 100%;
  font-size: 1.125rem;
  padding: 1rem 1.5rem;
  color: #fff;
  background-color: $primary;
  border-radius: 0.5rem;
  box-shadow: $personal-glow;
  cursor: pointer;
  transition: 0.2s ease-in-out;
  &:hover {
    opacity: 0.75;
  }
}

.todo-list {
  .list {
    margin: 1rem 0;
  }

  .todo-item {
    display: flex;
    align-items: center;
    background-color: #fff;
    padding: 1rem;
    border-radius: 0.5rem;
    box-shadow: $shadow;
    margin-bottom: 1rem;

    label {
      display: block;
      margin-right: 1rem;
      cursor: pointer;
    }

    .actions {
      display: flex;
      align-items: center;

      button {
        display: block;
        padding: 0.5rem;
        border-radius: 0.25rem;
        color: #fff;
        cursor: pointer;
        transition: 0.2s ease-in-out;

        &:hover {
          opacity: 0.75;
        }
      }

      .edit {
        margin-right: 0.5rem;
        background-color: $primary;
      }

      .delete {
        background-color: $danger;
      }
    }

    .todo-content input {
      text-decoration: line-through;
      color: $grey;
    }
  }
}

.todo-content {
  flex: 1 1 0%;

  input {
    color: $dark;
    font-size: 1.125rem;
  }
}
</style>
