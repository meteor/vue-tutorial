<script setup>
import Task from './components/Task.vue';
import TaskForm from './components/TaskForm.vue';
import { subscribe, autorun } from 'vue-meteor-tracker';
import { TasksCollection } from '../api/TasksCollection';

subscribe('tasks');
const tasks = autorun(() =>
  TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch(),
).result;
</script>

<template>
  <header
    class="flex items-center justify-between px-4 py-4 bg-gray-100 border-t border-b border-gray-200"
  >
    <h1 class="text-4xl font-bold text-gray-800 my-4">🚀 To-Do List</h1>
  </header>
  <div class="mx-auto max-w-2xl px-4 py-8 sm:px-6 lg:px-8">
    <div
      class="mb-8 md:w-96 md:mx-auto md:mb-0 md:mt-8 md:px-4 md:py-8 text-center md:bg-gray-100 md:rounded-lg"
    >
      <TaskForm />
      <ul class="list-none list-inside pt-4 md:w-96">
        <Task v-for="task of tasks" :key="task._id" :task="task" />
      </ul>
    </div>
  </div>
</template>
