<template>
  <li v-bind:class="taskClassName">
    <input
      type="checkbox"
      readOnly
      v-bind:checked="!!this.task.checked"
      @click="toggleChecked"
    />
    <span class="text">{{ this.task.text }}</span>
    <button class="delete" @click="deleteThisTask">
      ×
    </button>
  </li>
</template>

<script>
import { TasksCollection } from "../../api/TasksCollection";
export default {
  props: ["task"],
  data() {
    return {};
  },
  computed: {
    taskClassName: function() {
      return this.task.checked ? "checked" : "";
    }
  },
  methods: {
    toggleChecked() {
      // Set the checked property to the opposite of its current value
      TasksCollection.update(this.task._id, {
        $set: { checked: !this.task.checked }
      });
    },
    deleteThisTask() {
      TasksCollection.remove(this.task._id);
    }
  }
};
</script>
