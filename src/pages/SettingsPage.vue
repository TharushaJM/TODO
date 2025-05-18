<template>
    <q-page padding>
      <q-card class="q-pa-md">
        <div class="text-h6 q-mb-md">Settings</div>
  
        <q-toggle v-model="darkMode" label="Dark Mode" @update:model-value="toggleDark" />
      </q-card>
    </q-page>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { useQuasar } from 'quasar'
  
  const $q = useQuasar()
  const darkMode = ref($q.dark.isActive)
  
  function toggleDark() {
    $q.dark.set(darkMode.value)
  }
  </script>