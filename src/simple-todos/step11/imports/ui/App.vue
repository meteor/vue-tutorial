<template>
  <div class="app">
    <header>
      <div className="app-bar">
        <div className="app-header">
          <h1>
            📝️ To Do List
            <span v-if="incompleteCount > 0">({{incompleteCount}})</span>
          </h1>
        </div>
      </div>
    </header>

    <div class="main">
      <template v-if="currentUser">
        <div class="user" v-on:click="logout">
          {{currentUser.username}} 🚪
        </div>

        <TaskForm />

        <div class="filter">
          <button
              v-model="hideCompleted"
              @click="toggleHideCompleted"
          >
            <span v-if="hideCompleted">Show All</span>
            <span v-else>Hide Completed Tasks</span>
          </button>
        </div>

        <div class="loading" v-if="!$subReady.tasks">Loading...</div>

        <ul class="tasks">
          <Task
              class="task"
              v-for="task in tasks"
              v-bind:key="task._id"
              v-bind:task="task"
          />
        </ul>
      </template>

      <template v-else>
        <LoginForm />
      </template>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import { Meteor } from 'meteor/meteor';
import Task from "./components/Task.vue";
import TaskForm from "./components/TaskForm.vue";
import LoginForm from "./components/LoginForm";
import { TasksCollection } from "../db/TasksCollection";

export default {
  components: {
    Task,
    TaskForm,
    LoginForm
  },
  data() {
    return {
      hideCompleted: false
    };
  },
  methods: {
    toggleHideCompleted() {
      this.hideCompleted = !this.hideCompleted;
    },
    logout() {
      Meteor.logout();
    }
  },
  meteor: {
    $subscribe: {
      'tasks': []
    },
    tasks() {
      if (!this.currentUser) {
        return [];
      }

      const hideCompletedFilter = { isChecked: { $ne: true } };

      const userFilter = this.currentUser ? { userId: this.currentUser._id } : {};

      const pendingOnlyFilter = { ...hideCompletedFilter, ...userFilter };

      return TasksCollection.find(
          this.hideCompleted ? pendingOnlyFilter : userFilter,
          {
            sort: { createdAt: -1 },
          }
      ).fetch();
    },
    incompleteCount() {
      return TasksCollection.find({ isChecked: { $ne: true }, userId: this.currentUser._id }).count();
    },
    currentUser() {
      return Meteor.user();
    }
  }
};
</script>

<style></style>