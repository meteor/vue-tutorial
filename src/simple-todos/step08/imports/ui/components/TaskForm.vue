<script setup>
import { Meteor } from 'meteor/meteor'
import { ref } from 'vue'

const newTask = ref('')

const addTask = () => {
    Meteor.call('tasks.insert', newTask.value.trim())
    newTask.value = ''
}
</script>

<template>
    <form @submit.prevent="addTask">
        <input
            class=" border border-gray-300 rounded-md py-2 px-4 mr-2 text-gray-600 text-sm focus:outline-none focus:border-gray-400 focus:ring-0"
            type="text" v-model="newTask" placeholder="Type to add new tasks" />
        <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-1.5 px-4 rounded" type="submit">Add
            Task</button>
    </form>
</template>