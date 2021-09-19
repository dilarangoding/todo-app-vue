<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="Masukan todo list"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <transition-group
      enter-active-class="animate__animated animate__fadeInUp"
      leave-active-class="animate__animated animate__fadeOutDown"
    >
      <div
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        class="todo-item"
      >
        <div class="todo-item-left">
          <input type="checkbox" v-model="todo.completed" />
          <div
            v-if="!todo.editing"
            @dblclick="editTodo(todo)"
            class="todo-item-label"
            :class="{ completed: todo.completed }"
          >
            {{ todo.title }}
          </div>
          <input
            v-else
            type="text"
            v-model="todo.title"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @keyup.esc="cancelEdit(todo)"
            class="todo-item-edit"
            v-focus
          />
        </div>
        <div class="remove-item" @click="removeTodo(index)">&times;</div>
      </div>
    </transition-group>

    <div class="container-fluid">
      <div>
        <label>
          <input
            type="checkbox"
            v-bind:checked="!anyRemaining"
            @change="checkAllTodos"
          />
          Check All
        </label>
      </div>
      <div>{{ remaining }} items left</div>
    </div>

    <div class="container-fluid">
      <div>
        <button @click="filter = 'all'" :class="{ active: filter == 'all' }">
          All
        </button>
        <button
          @click="filter = 'active'"
          :class="{ active: filter == 'active' }"
        >
          Active
        </button>
        <button
          @click="filter = 'completed'"
          :class="{ active: filter == 'completed' }"
        >
          Completed
        </button>
      </div>
      <div>
        <transition name="fade">
          <button v-if="showClearComplete" @click="clearCompleted">
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoList",
  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforeEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Belajar Vue cli",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "Belajar NodeJS",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed);
      }

      return this.todos;
    },
    showClearComplete() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length === 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
      });

      this.newTodo = "";
      this.idForTodo++;
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    checkAllTodos() {
      this.todos.forEach((todo) => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
};
</script>

<style >
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css");
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
.todo-input:focus {
  outline: 0;
}

.todo-item {
  display: flex;
  margin-bottom: 12px;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
}

.remove-item:hover {
  color: black;
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  font-weight: "Avenir", Helvatica;
}
.todo-item-edit:focus {
  outline: none;
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.container-fluid {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}
button {
  font-size: 14px;
  background-color: white;
  padding: 5px;
  border: 1px solid rgb(148, 148, 148);
  margin-left: 4px;
}
button:hover {
  background: salmon;
}
button:focus {
  outline: none;
}
.active {
  color: white;
  background: salmon;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>