<template>
  <div class="week-scheduler">
    <table>
      <thead>
        <tr>
          <th></th>
          <th v-for="day in days" :key="day">{{ day }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="hour in hours" :key="hour">
          <td class="text-black">{{ hour }}:00</td>
          <td
            v-for="(slots, day) in schedule"
            :key="day"
            :class="{ selected: isSelected(day, hour) }"
            @mousedown="startSelection(day, hour)"
            @mouseup="endSelection(day, hour)"
            @mouseover="updateSelection(day, hour)"
          ></td>
        </tr>
        <tr>
          <td>All Day</td>
          <td v-for="day in days" :key="day" @click="toggleAllDay(day)" class="text-black">
            {{ isAllDaySelected(day) ? 'Deselect All' : 'Select All' }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'

const days = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
const hours = Array.from({ length: 24 }, (_, i) => i)

const schedule = reactive({
  Monday: [],
  Tuesday: [],
  Wednesday: [],
  Thursday: [],
  Friday: [],
  Saturday: [],
  Sunday : []
})

const isMouseDown = ref(false)
const startHour = ref(null)
const currentDay = ref(null)

const isSelected = (day, hour) => {
  return schedule[day].some(interval => hour >= interval.bt && hour < interval.et)
}

const startSelection = (day, hour) => {
  isMouseDown.value = true
  startHour.value = hour
  currentDay.value = day
  toggleHour(day, hour)
}

const endSelection = () => {
  isMouseDown.value = false
  startHour.value = null
  currentDay.value = null
}

const updateSelection = (day, hour) => {
  if (isMouseDown.value && currentDay.value === day) {
    toggleHour(day, hour)
  }
}

const toggleHour = (day, hour) => {
  const existingIndex = schedule[day].findIndex(interval => hour >= interval.bt && hour < interval.et)
  if (existingIndex !== -1) {
    schedule[day].splice(existingIndex, 1)
  } else {
    schedule[day].push({ bt: hour, et: hour + 1 })
  }
}

const toggleAllDay = (day) => {
  if (isAllDaySelected(day)) {
    schedule[day] = []
  } else {
    schedule[day] = [{ bt: 0, et: 24 }]
  }
}

const isAllDaySelected = (day) => {
  return schedule[day].length === 1 && schedule[day][0].bt === 0 && schedule[day][0].et === 24
}
</script>

<style scoped>
.week-scheduler {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  border-collapse: collapse;
}

table {
  width: 100%;
  border: 1px solid #ccc;
}

td {
  width: calc(100% / 11);
  height: 40px;
  cursor: pointer;
  border: 1px solid #ccc;
  background-color: #f9f9f9;
}
.text-black{
    color: black;
}
.selected {
  background-color: #4caf50;
}

.dragging {
  background-color: #81c784;
}
</style>