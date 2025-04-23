<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title>Task Manager</v-toolbar-title>
      <v-spacer />
      <v-btn text to="/">Accueil</v-btn>
      <v-btn text to="/tasks">Tâches</v-btn>
    </v-app-bar>

    <v-main>
      <v-container class="mt-5">
        <router-view />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts"></script>

<style scoped>
.v-container {
  max-width: 800px;
}
</style>
