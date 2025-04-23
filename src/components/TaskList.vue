<template>
  <v-list>
    <TaskItem
      v-for="task in tasks"
      :key="task.id"
      :task="task"
      @edit="$emit('edit', $event)"
      @delete="$emit('delete', $event)"
      @toggle="$emit('toggle', $event)"
    />
  </v-list>
</template>

<script setup lang="ts">
import type { Task } from '../types/Task'
import TaskItem from './TaskItem.vue'

defineProps<{ tasks: Task[] }>()
defineEmits(['edit', 'delete', 'toggle'])
</script>
