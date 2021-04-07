<template>
  <div class="app">
    <header>
      <div className="app-bar">
        <div className="app-header">
          <h1>📝️ To Do List</h1>
        </div>
      </div>
    </header>
    <div class="main">
      <TaskForm />
      <ul class="tasks">
        <Task
            class="task"
            v-for="task in tasks"
            v-bind:key="task._id"
            v-bind:task="task"
        />
      </ul>
    </div>
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