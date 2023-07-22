<script setup>
import { ref } from 'vue'
import Task from './components/Task.vue'
import TaskForm from './components/TaskForm.vue';
import { subscribe, autorun } from 'vue-meteor-tracker'
import { TasksCollection } from '../api/TasksCollection'

const hideCompleted = ref(false)

subscribe('tasks')
const tasks = autorun(() => {
  let filteredTasks = TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch()

  if (hideCompleted.value) {
    filteredTasks = filteredTasks.filter(task => !task.checked)
  }

  return filteredTasks
}).result

const incompleteTasksCount = autorun(() => {
  return TasksCollection.find({ checked: { $ne: true } }).count()
}).result

const toggleHideCompleted = () => {
  hideCompleted.value = !hideCompleted.value
}
</script>

<template>
  <header class="flex items-center justify-between px-4 py-4 bg-gray-100 border-t border-b border-gray-200">
    <h1 class="text-4xl font-bold text-gray-800 my-4">🚀 To-Do List
      <span v-if="incompleteTasksCount > 0" class="text-sm font-light text-gray-600">({{ incompleteTasksCount }})</span>
    </h1>
  </header>
  <div class="mx-auto max-w-2xl px-4 py-8 sm:px-6 lg:px-8">
    <div class="mb-8 md:w-96 md:mx-auto md:mb-0 md:mt-8 md:px-4 md:py-8 text-center md:bg-gray-100 md:rounded-lg">
      <TaskForm />
      <div>
        <button class="text-sm font-semibold text-gray-600 hover:text-gray-800" @click="toggleHideCompleted">
          <span v-if="hideCompleted">Show all</span>
          <span v-else>Hide completed</span>
        </button>
      </div>
      <ul class="list-none list-inside pr-4 pt-4 md:w-96">
        <Task v-for="task of tasks" :key="task._id" :task="task" />
      </ul>
    </div>
  </div>
</template>
