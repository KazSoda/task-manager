<template>
  <v-form @submit.prevent="submitForm" class="mb-6">
    <v-card class="pa-4">
      <v-text-field
        v-model="task.title"
        label="Titre"
        required
      />
      <v-textarea
        v-model="task.description"
        label="Description"
        auto-grow
        rows="2"
      />
      <v-btn type="submit" color="primary" class="mt-3" prepend-icon="mdi-plus">
        {{ isEditMode ? 'Modifier' : 'Ajouter' }}
      </v-btn>
    </v-card>
  </v-form>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import type { Task } from '../types/Task'

const props = defineProps<{
  initialTask: Task | null
}>()
const emit = defineEmits(['submit'])

const defaultTask = () => ({
  id: 0,
  title: '',
  description: '',
  completed: false,
})

const task = ref<Task>(defaultTask())

const isEditMode = ref(false)

watch(
  () => props.initialTask,
  (val) => {
    if (val) {
      task.value = { ...val }
      isEditMode.value = true
    } else {
      task.value = defaultTask()
      isEditMode.value = false
    }
  },
  { immediate: true }
)

function submitForm() {
  emit('submit', { ...task.value })
  task.value = defaultTask()
  isEditMode.value = false
}
</script>
