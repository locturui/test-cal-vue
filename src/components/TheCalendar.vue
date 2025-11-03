<template>
  <div class="cal">
    <div class="cal-header">
      <button @click="prevMonth"><Icon icon="tabler:arrow-left" /></button>
      <span>{{ months[month.getMonth()] }} {{ month.getFullYear() }}</span>
      <button @click="nextMonth"><Icon icon="tabler:arrow-right" /></button>
    </div>

    <div class="week-days">
      <div class="week-day" v-for="(day, i) in weekdays" :key="i">{{ day }}</div>
    </div>

    <div class="grid">
      <div
        v-for="(day, i) in days"
        :key="i"
        :class="[
          'day-cell',
          {
            selected: selected === formatDate(day),
          },
        ]"
        @click="selectDate(day)"
      >
        {{ day.getDate() }}
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Icon } from '@iconify/vue'
import { computed, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'

const { tm, locale } = useI18n({
  locale: 'en',
  messages: {
    en: {
      weekdays: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
    },
    ru: {
      weekdays: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'],
      months: ['Янв', 'Фев', 'Мар', 'Апр', 'Май', 'Июн', 'Июл', 'Авг', 'Сен', 'Окт', 'Ноя', 'Дек'],
    },
  },
})

function changeLocale(loc: 'en' | 'ru') {
  locale.value = loc
}

const weekdays = computed(() => tm('weekdays') as string[])
const months = computed(() => tm('months') as string[])

function formatDate(date: Date): string {
  let day = String(date.getDate())
  if (day.length === 1) {
    day = '0' + day
  }
  let month = String(date.getMonth() + 1)
  if (month.length === 1) {
    month = '0' + month
  }
  const year = date.getFullYear()
  return `${year}-${month}-${day}`
}

function goBackDays(date: Date, num: number): Date {
  const newDate = new Date(date)
  newDate.setDate(newDate.getDate() - num)
  return newDate
}

function daysInMonth(month: number, year: number): number {
  return new Date(year, month, 0).getDate()
}

const getMonthWithOverflow = (year: number, month: number) => {
  const firstDay = new Date(year, month, 1)
  const days: Date[] = []
  const dayCount = daysInMonth(month + 1, year)

  const startDay = firstDay.getDay()
  for (let i = 0; i < startDay; i++) {
    days.push(goBackDays(firstDay, startDay - i))
  }
  for (let i = 0; i < dayCount; i++) {
    days.push(new Date(year, month, firstDay.getDate() + i))
  }
  return days
}

const props = defineProps({
  defaultDate: String,
})

const emit = defineEmits<{
  changeDate: [date: string]
}>()

function prevMonth() {
  month.value = new Date(month.value.getFullYear(), month.value.getMonth() - 1, 1)
}

function nextMonth() {
  month.value = new Date(month.value.getFullYear(), month.value.getMonth() + 1, 1)
}

const selectDate = (date: Date) => {
  const dateStr = formatDate(date)
  selected.value = dateStr
  emit('changeDate', dateStr)
}

const month = ref<Date>(props.defaultDate ? new Date(props.defaultDate) : new Date())
const selected = ref<string>(props.defaultDate || '')

const days = computed(() => {
  return getMonthWithOverflow(month.value.getFullYear(), month.value.getMonth())
})

watch(
  () => props.defaultDate,
  (v) => {
    if (v) {
      month.value = new Date(v)
      selected.value = v
    }
  },
  { immediate: true },
)

defineExpose({
  changeLocale,
})
</script>

<style scoped>
.cal {
  max-width: 280px;
  margin: 0 auto;
  border: 1px solid #e0e0e0;
  padding: 12px;
  background: white;
}

.cal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.cal-header button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cal-header span {
  font-size: 16px;
}

.week-days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  margin-bottom: 4px;
}

.week-day {
  text-align: center;
  font-size: 11px;
  color: #757575;
  padding: 6px 0;
}

.grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 0;
}

.day-cell {
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  cursor: pointer;
  font-size: 13px;
  background: transparent;
}

.day-cell.selected {
  border: 1px solid #4a9eff;
}
</style>
