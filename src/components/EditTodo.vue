<template>
  <div class="edit-todo" key="editing">
    <input
      v-focus
      type="text"
      v-model="todo.task"
      @keyup.enter="$emit('done-edit', todo)"
      @blur="$emit('done-edit', todo)"
      @keyup.esc="$emit('cancel-edit', todo)"
    />
  </div>
</template>

<script>
export default {
  props: ["todo"],
  directives: {
    focus: {
      inserted(el) {
        el.focus();
      },
    },
  },
};
</script>

<style></style>
