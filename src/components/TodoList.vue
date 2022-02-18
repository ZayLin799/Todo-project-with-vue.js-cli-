<template>
  <li>
    <div class="todo-list">
      <transition name="fade" mode="out-in">
        <todo-item
          :todo="todo"
          v-if="!todo.editing"
          @remove-task="removedTodo"
          @edit-todo="$emit('edit-todo', $event)"
        />
        <edit-todo
          :todo="todo"
          v-else
          @done-edit="$emit('done-edit', $event)"
          @cancel-edit="$emit('cancel-edit', $event)"
        />
      </transition>
    </div>
  </li>
</template>

<script>
import EditTodo from "./EditTodo.vue";
import TodoItem from "./TodoItem.vue";
export default {
  components: { TodoItem, EditTodo },
  props: ["todo"],
  methods: {
    removedTodo(id) {
      this.$emit("remove-todo", id);
    },
  },
};
</script>

<style></style>
