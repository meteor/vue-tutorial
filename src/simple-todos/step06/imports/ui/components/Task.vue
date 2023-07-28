<script setup>
import { defineProps, ref, watch } from 'vue';
import { TasksCollection } from '../../api/TasksCollection';

const props = defineProps({
  task: Object
});

const taskRef = ref(props.task);

const deleteTask = () => {
  TasksCollection.remove(taskRef.value._id);
}

watch(
  () => taskRef.value.checked,
  (newCheckedValue) => {
    TasksCollection.update(taskRef.value._id, {
      $set: {
        checked: newCheckedValue
      }
    });
  },
  { immediate: true }
);
</script>

<template>
  <div class="flex items-center rounded p-4 py-2 mb-2 
    shadow-sm border border-gray-200
    md:mr-8
  ">
    <li>
      <input type="checkbox" readonly :checked="taskRef.checked" v-model="taskRef.checked" />
    </li>
    <span class="text-gray-600 pl-2" :class="{ 'text-gray-400 italic line-through': taskRef.checked }">
      {{ task.text }}
    </span>
    <button class="ml-auto bg-red-500 hover:bg-red-600 text-white font-bold py-0.5 px-2 rounded" @click="deleteTask">
      x
    </button>
  </div>
</template>
