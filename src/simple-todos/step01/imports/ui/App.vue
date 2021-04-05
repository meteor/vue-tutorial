<template>
  <div class="container">
    <header>
      <h1>Todo List</h1>
    </header>
    <ul>
      <Task
          v-for="task in getTasks()"
          v-bind:key="task._id"
          v-bind:task="task"
      />
    </ul>
  </div>
</template>

<script>
import Vue from "vue";
import Task from "./components/Task.vue";

export default {
  components: {
    Task
  },
  data() {
    return {};
  },
  methods: {
    getTasks() {
      return [
        { _id: 1, text: "This is task 1" },
        { _id: 2, text: "This is task 2" },
        { _id: 3, text: "This is task 3" }
      ];
    }
  }
};
</script>

<style></style>