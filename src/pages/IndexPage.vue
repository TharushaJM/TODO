<template>
  <q-page padding>
    <q-card class="q-pa-md">
      <div class="text-h6 q-mb-md">Task Manager</div>

      <!-- Task Inputs -->
      <q-input v-model="newTask.text" label="Task" filled class="q-mb-sm" />
      <q-input v-model="newTask.date" filled type="date" label="Due Date" class="q-mb-sm" />
      <q-input v-model="newTask.time" filled type="time" label="Due Time" class="q-mb-sm" />
      <q-btn label="Add Task" color="primary" class="q-mb-md" @click="addTask" />

      <q-separator class="q-my-md" />

      <!-- Task List -->
      <q-list bordered>
        <q-item v-for="(task, index) in tasks" :key="index">
          <q-item-section>
            <div>
              <span :class="{ 'text-strike': task.done }">{{ task.text }}</span><br />
              <small>{{ task.date }} at {{ task.time }}</small>
            </div>
          </q-item-section>

          <q-item-section avatar>
            <q-checkbox v-model="task.done" @update:model-value="saveTasks" />
          </q-item-section>

          <q-item-section side>
            <q-btn flat icon="alarm" @click="setAlarm(task)" />
            <q-btn flat icon="delete" color="red" @click="removeTask(index)" />
          </q-item-section>
        </q-item>
      </q-list>
    </q-card>
  </q-page>
</template>

<script setup>
import { reactive, ref, onMounted, watch } from 'vue'
import { Notify } from 'quasar'

// Reactive task input
const newTask = reactive({
  text: '',
  date: '',
  time: ''
})

// Task list
const tasks = ref([])

// Load saved tasks
onMounted(() => {
  const saved = localStorage.getItem('tasks')
  if (saved) {
    tasks.value = JSON.parse(saved)
  }
})

// Watch and save on change
watch(tasks, saveTasks, { deep: true })

// Add task
function addTask() {
  if (!newTask.text || !newTask.date || !newTask.time) {
    Notify.create({ message: 'All fields are required!', color: 'negative' })
    return
  }

  tasks.value.push({
    text: newTask.text,
    date: newTask.date,
    time: newTask.time,
    done: false
  })

  // Reset inputs
  newTask.text = ''
  newTask.date = ''
  newTask.time = ''

  saveTasks()
  Notify.create({ message: 'Task added!', color: 'positive' })
}

// Remove task
function removeTask(index) {
  tasks.value.splice(index, 1)
  saveTasks()
}

// Set alarm
function setAlarm(task) {
  const [hours, minutes] = task.time.split(':')
  const alarmDate = new Date(task.date)
  alarmDate.setHours(+hours, +minutes, 0)

  const now = new Date()
  const timeUntilAlarm = alarmDate.getTime() - now.getTime()

  if (timeUntilAlarm > 0) {
    setTimeout(() => {
      Notify.create({
        message: `⏰ Reminder: ${task.text}`,
        color: 'primary',
        timeout: 10000
      })
    }, timeUntilAlarm)
    Notify.create({ message: 'Alarm set!', color: 'positive' })
  } else {
    Notify.create({ message: 'Time already passed', color: 'warning' })
  }
}

// Save tasks
function saveTasks() {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}
</script>

<style scoped>
.text-strike {
  text-decoration: line-through;
}
</style>
