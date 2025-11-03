<script setup lang="ts">
import { ref } from 'vue'
import TheCalendar from './components/TheCalendar.vue'

const selectedDate = ref('2025-11-02')
const calendar = ref<InstanceType<typeof TheCalendar> | null>(null)

const handleChangeDate = (date: string) => {
  selectedDate.value = date
}

const changeLocale = (loc: 'en' | 'ru') => {
  calendar.value?.changeLocale(loc)
}
</script>

<template>
  <div>
    <TheCalendar ref="calendar" :defaultDate="selectedDate" @changeDate="handleChangeDate" />
    <div class="buttons">
      <button @click="changeLocale('en')">English</button>
      <button @click="changeLocale('ru')">Русский</button>
    </div>
    <p v-if="selectedDate">Selected: {{ selectedDate }}</p>
  </div>
</template>

<style scoped>
button {
  padding: 8px 16px;
  border: 1px solid #e0e0e0;
  background: #fff;
  cursor: pointer;
  border-radius: 4px;
  font-size: 14px;
}

button:hover {
  background: #f5f5f5;
}

.buttons {
  margin-top: 16px;
  display: flex;
  gap: 8px;
  justify-content: center;
}
</style>
