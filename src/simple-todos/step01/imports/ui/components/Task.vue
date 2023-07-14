<template>
  <li>{{ task.text }}</li>
</template>

<script>
import { defineProps } from 'vue'

export default {
  props: ['task'],
  setup(props) {
    return defineProps(props)
  },
}
</script>
