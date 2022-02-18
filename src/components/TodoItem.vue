<template>
  <div class="todo-item" key="showing">
    <div class="todo-left-item">
      <input type="checkbox" v-model="todo.completed" />
      <p
        @dblclick="$emit('edit-todo', todo)"
        :class="{ completed: todo.completed }"
      >
        {{ todo.task }}
      </p>
    </div>
    <div class="remove-item" @click.stop="$emit('remove-task', todo.id)">x</div>
    <!--removeTodo(todo.id) -->
  </div>
</template>

<script>
export default {
  props: ["todo"],
};
</script>

<style></style>
