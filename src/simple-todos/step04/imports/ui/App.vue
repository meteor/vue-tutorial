<script setup>
import Task from './components/Task.vue'
import TaskForm from './components/TaskForm.vue';
import { subscribe, autorun } from 'vue-meteor-tracker'
import { TasksCollection } from '../api/TasksCollection'

subscribe('tasks')
const tasks = autorun(() => TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch()).result
</script>

<template>
  <div class="container">
    <header>
      <h1 class="text-4xl font-bold text-gray-800 my-4">Todo List</h1>
    </header>
    <TaskForm />
    <ul class="list-disc list-inside p-4 md:w-96">
      <Task v-for="task of tasks" :key="task._id" :task="task" />
    </ul>
  </div>
</template>
