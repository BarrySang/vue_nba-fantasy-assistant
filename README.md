# NBA Fantasy Planner

A lightweight Vue 3 app that helps you choose a fantasy-friendly team distribution for each NBA game week. The app reads a schedule from a local JSON file, groups games into weekly ranges, and recommends how to allocate a fixed 10-player roster across teams based on how many games they have in the selected week.

## What it does

- Displays a week selector built from the available NBA schedule data
- Groups the season into Monday-Sunday game weeks automatically
- Counts how many games each team plays during the selected week
- Recommends a team distribution for a 10-player fantasy roster using a proportional, greedy allocation model
- Shows the current distribution in a clean dark-themed dashboard

This is a front-end planning tool only. It does not fetch live NBA data, manage accounts, or generate real player lineups. The schedule is currently sourced from a local data file.

## Current functionality

### Week-based planning
The app loads the schedule from [schedule.json](schedule.json) and derives weekly ranges from the earliest and latest game dates. Each week is displayed in a dropdown, and the selected week changes the games used for recommendations.

### Team recommendation logic
Each selected week's games are aggregated by team abbreviation. The app then:

- counts games for each team
- sorts teams by game count descending
- allocates 10 roster slots proportionally to the teams with the most games
- ensures each team gets at least one slot when it has games
- distributes remaining slots to the top teams until the roster is full

The result is then rendered as a list of team abbreviations, names, game counts, and recommended player counts.

## Tech stack

- Vue 3
- Vite
- Tailwind CSS
- Static JSON schedule data

## Project structure

- [src/App.vue](src/App.vue) - main app layout and week selection logic
- [src/components/WeekSelector.vue](src/components/WeekSelector.vue) - week dropdown UI
- [src/components/TeamRecommendation.vue](src/components/TeamRecommendation.vue) - team distribution recommendation logic and display
- [schedule.json](schedule.json) - source dataset of NBA games used by the planner
- [package.json](package.json) - scripts and dependencies

## Getting started

### Prerequisites

- Node.js 18+ recommended
- npm

### Install dependencies

```bash
npm install
```

### Run locally

```bash
npm run dev
```

Then open the local Vite URL shown in the terminal, typically:

```text
http://localhost:5173/
```

### Production build

```bash
npm run build
```

This creates a production bundle in the `dist` folder.

## Notes

- The app is intentionally simple and uses a static schedule file instead of a backend.
- The recommendation model is a heuristic based on game count, not a full fantasy-optimization engine.
- If you want to adjust the schedule, update the entries in [schedule.json](schedule.json) in the same structure used by the app.

## Example data format

```json
[
  {
    "date": "2025-10-21",
    "home": "OKC",
    "away": "HOU"
  }
]
```

Each item represents one NBA game with:

- `date`: ISO date string (`YYYY-MM-DD`)
- `home`: home team abbreviation
- `away`: away team abbreviation
