<template>
  <div className="container">
    <header>
      <h1>Todo List</h1>
    </header>
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
import { TasksCollection } from "../api/TasksCollection";

export default {
  components: {
    Task
  },
  data() {
    return {};
  },
  methods: {},
  meteor: {
    tasks() {
      return TasksCollection.find({}).fetch();
    }
  }
};
</script>

<style></style>