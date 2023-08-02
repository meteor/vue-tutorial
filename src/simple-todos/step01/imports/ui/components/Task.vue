<script setup>
import { defineProps } from 'vue';

defineProps(['task']);
</script>

<template>
  <li>{{ task.text }}</li>
</template>
