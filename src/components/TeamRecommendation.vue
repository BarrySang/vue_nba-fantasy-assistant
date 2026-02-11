<script setup>
import { computed } from 'vue'

const props = defineProps({
  games: Array,
})

const TOTAL_PLAYERS = 10

const teamAbbreviationToName = {
  ATL: 'Hawks', BOS: 'Celtics', BKN: 'Nets', CHA: 'Hornets', CHI: 'Bulls',
  CLE: 'Cavaliers', DAL: 'Mavericks', DEN: 'Nuggets', DET: 'Pistons',
  GSW: 'Warriors', HOU: 'Rockets', IND: 'Pacers', LAC: 'Clippers',
  LAL: 'Lakers', MEM: 'Grizzlies', MIA: 'Heat', MIL: 'Bucks',
  MIN: 'Timberwolves', NOP: 'Pelicans', NYK: 'Knicks', OKC: 'Thunder',
  ORL: 'Magic', PHI: '76ers', PHX: 'Suns', POR: 'Trail Blazers',
  SAC: 'Kings', SAS: 'Spurs', TOR: 'Raptors', UTA: 'Jazz', WAS: 'Wizards',
}

const recommendation = computed(() => {
  if (!props.games.length) return []

  // Count games per team
  const counts = {}
  for (const game of props.games) {
    counts[game.home] = (counts[game.home] || 0) + 1
    counts[game.away] = (counts[game.away] || 0) + 1
  }

  // Sort teams by game count descending
  const sorted = Object.entries(counts)
    .map(([abbr, gameCount]) => ({ abbr, name: teamAbbreviationToName[abbr] || abbr, gameCount }))
    .sort((a, b) => b.gameCount - a.gameCount)

  // Distribute players proportionally to teams with the most games
  // Greedy approach: give more players to teams with more games
  const distribution = []
  let remaining = TOTAL_PLAYERS

  // Calculate total games across selected teams
  // We pick teams greedily until we fill 10 slots
  const totalGames = sorted.reduce((sum, t) => sum + t.gameCount, 0)

  for (const team of sorted) {
    if (remaining <= 0) break
    // Proportional share, at least 1 player for teams with games
    let players = Math.round((team.gameCount / totalGames) * TOTAL_PLAYERS)
    players = Math.max(1, Math.min(players, remaining))
    distribution.push({ ...team, players })
    remaining -= players
  }

  // If we still have remaining spots, add to top teams
  let i = 0
  while (remaining > 0) {
    distribution[i % distribution.length].players++
    remaining--
    i++
  }

  return distribution
})

const totalGamesThisWeek = computed(() => props.games.length)
</script>

<template>
  <div v-if="!games.length" class="text-center text-gray-400 py-8">
    No games scheduled for this week.
  </div>

  <div v-else>
    <div class="mb-6 text-center">
      <p class="text-gray-400 text-sm">
        {{ totalGamesThisWeek }} games this week
      </p>
    </div>

    <h3 class="text-lg font-semibold text-white mb-4">
      Recommended Team Distribution
    </h3>

    <div class="space-y-3">
      <div
        v-for="team in recommendation"
        :key="team.abbr"
        class="flex items-center justify-between bg-gray-700 rounded-lg px-4 py-3"
      >
        <div class="flex items-center gap-3">
          <span class="text-xs font-bold text-gray-400 w-10">{{ team.abbr }}</span>
          <span class="text-white font-medium">{{ team.name }}</span>
          <span class="text-gray-400 text-sm">({{ team.gameCount }} games)</span>
        </div>
        <span class="bg-blue-600 text-white text-sm font-bold px-3 py-1 rounded-full">
          {{ team.players }} {{ team.players === 1 ? 'player' : 'players' }}
        </span>
      </div>
    </div>

    <div class="mt-6 pt-4 border-t border-gray-600 text-center">
      <p class="text-gray-300 text-sm">
        Total: <span class="text-white font-bold">{{ TOTAL_PLAYERS }} players</span>
      </p>
    </div>
  </div>
</template>
