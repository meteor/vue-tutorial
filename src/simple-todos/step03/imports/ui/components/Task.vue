<template>
  <li>{{ this.task.text }}</li>
</template>

<script>
export default {
  props: ["task"],
  data() {
    return {};
  }
};
</script>
