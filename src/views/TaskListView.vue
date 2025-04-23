<template>
  <v-container>
    <h2 class="text-h4 mb-4">Liste des tâches</h2>

    <!-- Formulaire -->
    <TaskForm @submit="handleSubmit" :initialTask="taskToEdit" />

    <!-- Barre d’outils -->
    <v-row class="mb-4" align="center">
      <v-col cols="12" md="6">
        <v-text-field v-model="search" label="Rechercher une tâche" prepend-icon="mdi-magnify" />
      </v-col>

      <v-col cols="12" md="6">
        <v-select
  v-model="sortBy"
  :items="sortOptions"
  item-title="text"
  item-value="value"
  label="Trier par"
  prepend-icon="mdi-sort"
/>
      </v-col>
    </v-row>

    <!-- Liste des tâches filtrée et triée -->
    <TaskList :tasks="filteredTasks" @edit="handleEdit" @delete="confirmDelete" @toggle="handleToggle" />

    <!-- Dialogue de confirmation de suppression -->
    <v-dialog v-model="showConfirm" max-width="400">
      <v-card>
        <v-card-title>Confirmer la suppression</v-card-title>
        <v-card-text>Voulez-vous vraiment supprimer cette tâche ?</v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text @click="showConfirm = false">Annuler</v-btn>
          <v-btn color="red" text @click="handleDelete">Supprimer</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import TaskForm from '../components/TaskForm.vue'
import TaskList from '../components/TaskList.vue'
import type { Task } from '../types/Task'

const tasks = ref<Task[]>([])
const taskToEdit = ref<Task | null>(null)
const search = ref('')

const sortBy = ref<'date' | 'etat'>('date')

const sortOptions = [
  { text: 'Date d’ajout', value: 'date' },
  { text: 'État (fait/non fait)', value: 'etat' },
]


// 🔁 LocalStorage
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

// ➕ Ajouter ou modifier
function handleSubmit(task: Task) {
  if (taskToEdit.value) {
    const index = tasks.value.findIndex((t) => t.id === task.id)
    if (index !== -1) tasks.value[index] = task
    taskToEdit.value = null
  } else {
    task.id = Date.now()
    task.completed = false
    tasks.value.push(task)
  }
}

// ✏️ Modifier
function handleEdit(task: Task) {
  taskToEdit.value = { ...task }
}

// ✅ Toggle completed
function handleToggle(updated: Task) {
  const t = tasks.value.find((task) => task.id === updated.id)
  if (t) {
    t.completed = updated.completed
  }
}

// 🗑️ Suppression avec confirmation
const showConfirm = ref(false)
let taskIdToDelete: number | null = null

function confirmDelete(id: number) {
  taskIdToDelete = id
  showConfirm.value = true
}

function handleDelete() {
  if (taskIdToDelete !== null) {
    tasks.value = tasks.value.filter((task) => task.id !== taskIdToDelete)
    taskIdToDelete = null
    showConfirm.value = false
  }
}

// 🔍 Recherche + Tri
const filteredTasks = computed(() => {
  let result = tasks.value.filter((t) =>
    t.title.toLowerCase().includes(search.value.toLowerCase())
  )

  if (sortBy.value === 'date') {
    result = result.sort((a, b) => b.id - a.id)
  } else if (sortBy.value === 'etat') {
    result = result.sort((a, b) => Number(a.completed) - Number(b.completed))
  }

  return result
})
</script>
