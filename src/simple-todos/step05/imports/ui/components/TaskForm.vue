<template>
  <form class="task-form" @submit.prevent="handleSubmit">
    <input
        type="text"
        placeholder="Type to add new tasks"
        v-model="newTask"
    />
    <button type="submit">Add Task</button>
  </form>
</template>

<script>
import Vue from "vue";
import { TasksCollection } from "../../api/TasksCollection";

export default {
  data() {
    return {
      newTask: ""
    }
  },
  methods: {
    handleSubmit(event) {
      TasksCollection.insert({
        text: this.newTask,
        createdAt: new Date() // current time
      });

      // Clear form
      this.newTask = "";
    }
  },
}
</script>