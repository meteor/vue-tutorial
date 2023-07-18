<script setup>
import Task from './components/Task.vue'
import { subscribe, autorun } from 'vue-meteor-tracker'
import { TasksCollection } from '../api/TasksCollection'

subscribe('tasks')
const tasks = autorun(() => TasksCollection.find({}).fetch()).result
</script>

<template>
  <div class="container">
    <header>
      <h1 class="text-4xl font-bold text-gray-800 my-4">Todo List</h1>
    </header>
    <ul class="list-disc list-inside p-4">
      <Task v-for="task of tasks" :key="task._id" :task="task" />
    </ul>
  </div>
</template>
