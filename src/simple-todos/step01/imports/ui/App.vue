<script setup>
import Task from './components/Task.vue'

const getTasks = () => {
  return [
    { _id: 1, text: 'This is task 1' },
    { _id: 2, text: 'This is task 2' },
    { _id: 3, text: 'This is task 3' },
  ]
}
</script>

<template>
  <div class="container">
    <header>
      <h1 class="text-4xl font-bold text-gray-800 my-4">Todo List</h1>
    </header>
    <ul class="list-disc list-inside p-4">
      <Task v-for="task in getTasks()" :key="task._id" :task="task" />
    </ul>
  </div>
</template>
