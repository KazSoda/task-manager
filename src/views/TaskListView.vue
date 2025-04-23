<template>
    <v-container>
      <h2 class="text-h4 mb-4">Liste des tâches</h2>
  
      <TaskForm @submit="handleSubmit" :initialTask="taskToEdit" />
  
      <TaskList
        :tasks="tasks"
        @edit="handleEdit"
        @delete="handleDelete"
        @toggle="handleToggle"
      />
    </v-container>
  </template>
  
  <script setup lang="ts">
  import { ref, watch, onMounted } from 'vue'
  import TaskForm from '../components/TaskForm.vue'
  import TaskList from '../components/TaskList.vue'
  import type { Task } from '../types/Task'
  
  const tasks = ref<Task[]>([])
  
  const taskToEdit = ref<Task | null>(null)
  
  onMounted(() => {
    const saved = localStorage.getItem('tasks')
    if (saved) {
      tasks.value = JSON.parse(saved)
    }
  })
  
  watch(
    tasks,
    (newTasks) => {
      localStorage.setItem('tasks', JSON.stringify(newTasks))
    },
    { deep: true }
  )
  
  function handleSubmit(task: Task) {
    if (taskToEdit.value) {
      // Modification d’une tâche existante
      const index = tasks.value.findIndex((t) => t.id === task.id)
      if (index !== -1) tasks.value[index] = task
      taskToEdit.value = null
    } else {
      // Ajout d’une nouvelle tâche
      task.id = Date.now()
      task.completed = false
      tasks.value.push(task)
    }
  }
  
  function handleEdit(task: Task) {
    taskToEdit.value = { ...task } // clone pour édition
  }
  
  function handleDelete(id: number) {
    tasks.value = tasks.value.filter((task) => task.id !== id)
  }
  
  function handleToggle(updated: Task) {
    const t = tasks.value.find((task) => task.id === updated.id)
    if (t) {
      t.completed = updated.completed
    }
  }
  </script>
  