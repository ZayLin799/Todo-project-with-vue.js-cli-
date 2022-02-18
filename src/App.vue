<template>
  <div class="container">
    <div class="add-todo">
      <transition name="fade" mode="out-in">
        <div class="add-input" key="addInput" v-if="!searchFeature">
          <span @click="allCompletedTask">
            <i
              class="fas fa-chevron-down"
              :class="{ allCompleted: allCompletedTodo }"
            ></i>
          </span>
          <input
            type="text"
            @keyup.enter="addTodo"
            v-model="newTodo"
            placeholder="What need to be done?"
          />
          <span @click="searchFeature = true">
            <i class="fas fa-search search-icon"></i>
          </span>
        </div>
        <div class="search-input" key="searchTask" v-else>
          <input
            type="text"
            v-focus
            placeholder="Search Task...."
            v-model="search"
          />
          <button @click="searchFeature = false">Add Task</button>
        </div>
      </transition>
    </div>
    <ul>
      <transition-group name="slide">
        <todo-list
          v-for="todo in searchTodos"
          :key="todo.id"
          :todo="todo"
          @remove-task="removeTodo"
          @remove-todo="removeTodo"
          @edit-todo="editTodo"
          @done-edit="doneEdit"
          @cancel-edit="cancelEdit"
        />
      </transition-group>
    </ul>
    <div class="filter-task-container">
      <div class="left-task">
        <p>{{ remainderTodo }} item left</p>
      </div>
      <div class="filter-task-button">
        <button
          @click="filter = 'all'"
          :class="{ currentTodos: filter === 'all' }"
        >
          All
        </button>
        <button
          @click="filter = 'active'"
          :class="{ currentTodos: filter === 'active' }"
        >
          Active
        </button>
        <button
          @click="filter = 'completed'"
          :class="{ currentTodos: filter === 'completed' }"
        >
          Completed
        </button>
      </div>
      <transition name="fade">
        <div class="clear-completed-button" v-if="showClearCompleted">
          <button @click="clearCompleted">Clear Completed</button>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import TodoList from "./components/TodoList.vue";
export default {
  name: "App",
  components: {
    "todo-list": TodoList,
  },
  data() {
    return {
      newTodo: "",
      search: "",
      searchFeature: false,
      editTaskCache: "",
      allCompleted: true,
      filter: "all",
      id: 4,
      todos: [
        {
          id: 1,
          task: "Homework",
          completed: true,
          editing: false,
        },
        {
          id: 2,
          task: "Go to School",
          completed: false,
          editing: false,
        },
        {
          id: 3,
          task: "Go to Shopping",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  directives: {
    focus: {
      inserted(el) {
        el.focus();
      },
    },
  },
  computed: {
    remainderTodo() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    allCompletedTodo() {
      return this.remainderTodo === 0;
    },
    filterTodos() {
      if (this.filter === "active") {
        return this.todos.filter((todo) => !todo.completed);
      }
      if (this.filter === "completed") {
        return this.todos.filter((todo) => todo.completed);
      }
      return this.todos;
    },
    showClearCompleted() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
    searchTodos() {
      if (this.search.trim() !== "") {
        return this.filterTodos.filter(
          (todo) =>
            todo.task.toLowerCase().indexOf(this.search.toLowerCase()) > -1
        );
      }
      return this.filterTodos;
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim() === "") {
        return;
      }
      let todo = {
        id: this.id,
        task: this.newTodo,
        completed: false,
        editing: false,
      };
      this.todos.push(todo);
      this.newTodo = "";
      this.id++;
    },
    removeTodo(id) {
      this.todos = this.todos.filter((todo) => todo.id !== id);
      // let index = this.todos.findIndex((todo) => todo.id === id);
      // this.todos.splice(index, 1);
    },
    completedTodo(todo) {
      todo.completed = !todo.completed;
    },
    editTodo(todo) {
      this.editTaskCache = todo.task;
      this.todos.forEach((todo) => {
        todo.editing = false;
      });
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.task.trim() === "") {
        todo.task = this.editTaskCache;
      }
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.task = this.editTaskCache;
      todo.editing = false;
    },
    allCompletedTask() {
      if (this.remainderTodo === 0) {
        this.allCompleted = true;
      } else {
        this.allCompleted = false;
      }
      this.todos.forEach((todo) => {
        todo.completed = !this.allCompleted;
      });
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-size: 24px;
  background-color: #f5f5f5;
}
.container {
  max-width: 450px;
  margin: 0 auto;
  background-color: #ffffff;
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.055);
}
.add-todo {
  margin-top: 50px;
  background-color: #ffffff;
  box-shadow: inset 0 -2px 2px 0 rgba(0, 0, 0, 0.055);
  display: flex;
}
.add-input {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}
.search-icon {
  color: rgba(0, 0, 0, 0.507);
  cursor: pointer;
}
.add-todo span {
  padding: 10px 13px;
}
.add-todo input {
  font-size: 24px;
  flex-grow: 1;
  outline: none;
  background: none;
  border: none;
  z-index: 20;
}
.search-input {
  display: flex;
  width: 100%;
}
.search-input button {
  padding: 0 10px;
}
.search-input input {
  padding: 11.5px 13px;
}
li {
  list-style: none;

  border-bottom: 1px solid #eeeeee;
}
.todo-item {
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.todo-left-item {
  display: flex;
  flex: 1;
  align-items: center;
}
.todo-item:hover .remove-item {
  opacity: 1;
}
.todo-left-item input {
  margin-left: 10px;
}
.remove-item {
  padding: 10px 15px 10px 0;
  font-weight: bold;
  color: rgba(255, 0, 0, 0.315);
  cursor: pointer;
  opacity: 0;
}
.todo-item p {
  padding: 10px 15px;
  flex-grow: 1;
  width: 100%;
  transition: 0.2s;
}
.remove-item:hover {
  color: red;
}
.completed {
  transition: 0.2s;
  text-decoration: line-through;
  opacity: 0.5;
}
.edit-todo {
  padding-left: 25px;
  width: 100%;
}
.edit-todo input {
  width: 100%;
  font-size: 24px;
  outline: none;
  background: none;
  border: none;
  padding: 10px 13px;
  box-shadow: inset 0 0 5px 0 rgba(0, 0, 0, 0.192);
}
.filter-task-container {
  padding: 10px 15px;
  font-size: 13px;
  display: flex;
  align-items: center;
}
.filter-task-container div {
  width: 33.333%;
}
.filter-task-button {
  display: flex;
}
.filter-task-button > button {
  margin-right: 5px;
  font-size: 13px;
  background-color: none;
  outline: none;
  border: none;
  padding: 3px 5px;
  cursor: pointer;
}
.clear-completed-button {
  text-align: end;
  transition: 0.5s;
}
.clear-completed-button button {
  font-size: 13px;
  background-color: none;
  outline: none;
  border: none;
  cursor: pointer;
}
.clear-completed-button button:hover {
  text-decoration: underline;
}
span i {
  transition: 0.5s;
  color: #dfdfdf;
}
.allCompleted {
  color: black;
}
.currentTodos {
  border: 1px solid #000000 !important;
}
.slide-enter-active,
.slide-leave-active {
  transition: opacity 0.5s, transform 0.5s;
}
.slide-enter {
  transform: translateX(-100px);
  opacity: 0;
}
.slide-leave-to {
  transform: translateX(100px);
  opacity: 0;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
