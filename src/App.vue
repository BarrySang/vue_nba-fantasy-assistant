<script setup>
import { ref, computed } from 'vue'
import WeekSelector from './components/WeekSelector.vue'
import TeamRecommendation from './components/TeamRecommendation.vue'
import schedule from '../schedule.json'

// Build Monday-Sunday weeks from the schedule date range
function buildWeeks(games) {
  const dates = games.map((g) => g.date).sort()
  const first = new Date(dates[0] + 'T00:00:00')
  const last = new Date(dates[dates.length - 1] + 'T00:00:00')

  // Find the Monday on or before the first game
  const startMonday = new Date(first)
  startMonday.setDate(startMonday.getDate() - startMonday.getDay() + 1)
  if (startMonday > first) startMonday.setDate(startMonday.getDate() - 7)

  const weeks = []
  const current = new Date(startMonday)

  while (current <= last) {
    const weekStart = current.toISOString().split('T')[0]
    const weekEnd = new Date(current)
    weekEnd.setDate(weekEnd.getDate() + 6)
    weeks.push({ start: weekStart, end: weekEnd.toISOString().split('T')[0] })
    current.setDate(current.getDate() + 7)
  }

  return weeks
}

const weeks = buildWeeks(schedule)
const selectedWeekIndex = ref(0)

const selectedWeek = computed(() => weeks[selectedWeekIndex.value])

const gamesThisWeek = computed(() =>
  schedule.filter(
    (g) => g.date >= selectedWeek.value.start && g.date <= selectedWeek.value.end
  )
)
</script>

<template>
  <div class="min-h-screen bg-gray-900 text-white">
    <div class="max-w-2xl mx-auto px-4 py-8">
      <h1 class="text-3xl font-bold text-center mb-2">NBA Fantasy Planner</h1>
      <p class="text-gray-400 text-center mb-8">
        Pick the best team distribution for your game week
      </p>

      <div class="bg-gray-800 rounded-xl p-6 mb-6">
        <WeekSelector
          :weeks="weeks"
          v-model:selectedWeekIndex="selectedWeekIndex"
        />
      </div>

      <div class="bg-gray-800 rounded-xl p-6">
        <TeamRecommendation :games="gamesThisWeek" />
      </div>
    </div>
  </div>
</template>
