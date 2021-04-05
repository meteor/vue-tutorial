<template>
  <div class="container">
    <header>
      <h1>Todo List</h1>
    </header>
    <TaskForm />
    <ul>
      <Task
          v-for="task in tasks"
          v-bind:key="task._id"
          v-bind:task="task"
      />
    </ul>
  </div>
</template>

<script>
import Vue from "vue";
import Task from "./components/Task.vue";
import TaskForm from "./components/TaskForm.vue";
import { TasksCollection } from "../api/TasksCollection";

export default {
  components: {
    Task,
    TaskForm
  },
  data() {
    return {};
  },
  methods: {},
  meteor: {
    tasks() {
      return TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch();
    }
  }
};
</script>

<style></style>