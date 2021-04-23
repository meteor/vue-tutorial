<template>
  <li v-bind:class="taskClassName">
    <input
      type="checkbox"
      readOnly
      v-bind:checked="!!this.task.isChecked"
      @click="toggleChecked"
    />
    <span class="text">{{ this.task.text }}</span>
    <button class="delete" @click="deleteThisTask">
      ×
    </button>
  </li>
</template>

<script>
import { Meteor } from 'meteor/meteor';
export default {
  props: ["task"],
  data() {
    return {};
  },
  computed: {
    taskClassName: function() {
      return this.task.isChecked ? "checked" : "";
    }
  },
  methods: {
    toggleChecked() {
      // Set the checked property to the opposite of its current value
      Meteor.call('tasks.setIsChecked', this.task._id, !this.task.isChecked);
    },
    deleteThisTask() {
      Meteor.call('tasks.remove', this.task._id);
    }
  }
};
</script>
