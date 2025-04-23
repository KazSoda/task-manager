<template>
  <v-card class="mb-2" :color="task.completed ? 'green-lighten-4' : 'white'">
    <v-card-title class="d-flex align-center">
      <v-checkbox
        v-model="isChecked"
        @change="toggleCompleted"
        hide-details
        class="me-3"
      />
      <div class="task-title">
        <span :class="{ done: task.completed }">{{ task.title }}</span>
        <div class="text-body-2">{{ task.description }}</div>
      </div>
      <v-spacer />
      <v-btn icon @click="$emit('edit', task)">
        <v-icon>mdi-pencil</v-icon>
      </v-btn>
      <v-btn icon @click="$emit('delete', task.id)">
        <v-icon>mdi-delete</v-icon>
      </v-btn>
    </v-card-title>
  </v-card>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import type { Task } from '../types/Task'

const props = defineProps<{ task: Task }>()
const emit = defineEmits(['edit', 'delete', 'toggle'])

const isChecked = ref(props.task.completed)

watch(
  () => props.task.completed,
  (newVal) => {
    isChecked.value = newVal
  }
)

function toggleCompleted() {
  emit('toggle', { ...props.task, completed: isChecked.value })
}
</script>

<style scoped>
.done {
  text-decoration: line-through;
  color: #4caf50;
}
.task-title {
  flex-grow: 1;
}
</style>
