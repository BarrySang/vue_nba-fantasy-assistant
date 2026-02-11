<script setup>
import { computed } from 'vue'

const props = defineProps({
  weeks: Array,
  selectedWeekIndex: Number,
})

const emit = defineEmits(['update:selectedWeekIndex'])

const formattedWeeks = computed(() =>
  props.weeks.map((week, i) => {
    const start = new Date(week.start + 'T00:00:00')
    const end = new Date(week.end + 'T00:00:00')
    const fmt = (d) =>
      d.toLocaleDateString('en-US', { month: 'short', day: 'numeric' })
    return `Week ${i + 1}: ${fmt(start)} - ${fmt(end)}`
  })
)
</script>

<template>
  <div>
    <label for="week-select" class="block text-sm font-medium text-gray-300 mb-2">
      Select Game Week
    </label>
    <select
      id="week-select"
      :value="selectedWeekIndex"
      @change="emit('update:selectedWeekIndex', Number($event.target.value))"
      class="w-full bg-gray-700 border border-gray-600 text-white rounded-lg px-4 py-3 text-sm focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none"
    >
      <option v-for="(label, i) in formattedWeeks" :key="i" :value="i">
        {{ label }}
      </option>
    </select>
  </div>
</template>
