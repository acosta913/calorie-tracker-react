# Calorie Tracker

A small single-page web app to log food and exercise and see your net calories for the day. Built with React 19, TypeScript and Vite. Data persists in the browser via `localStorage`, so no backend is required.

> También disponible en español: [README.es.md](./README.es.md)

## Tech stack

- **React 19** with **TypeScript** (strict mode)
- **Vite 6** with the SWC plugin for fast HMR
- **Tailwind CSS 4** through the official Vite plugin (no PostCSS config needed)
- **Heroicons** for the edit/delete icons
- **uuid** for generating activity identifiers

## What it does

- Add an entry as either *Comida* (food, counts toward calories consumed) or *Ejercicio* (exercise, counts toward calories burned).
- See a live summary of calories consumed, burned, and the net difference.
- Edit or delete any entry directly from the list.
- Reset the whole log with one click.
- Entries are saved automatically. Closing and reopening the tab keeps your data.

The UI is in Spanish, which is intentional and matches the original product copy.

## Getting started

Requirements: Node.js 18 or newer and npm.

```bash
npm install
npm run dev
```

Then open the URL printed in the terminal (usually `http://localhost:5173`).

| Script | Purpose |
| --- | --- |
| `npm run dev` | Start the dev server with HMR |
| `npm run build` | Type-check with `tsc -b` and build for production |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Run ESLint over the project |

The production build fails if TypeScript reports any error.

## Project structure

```
src/
├── components/
│   ├── ActivityList.tsx     // Renders the list and the edit/delete buttons
│   ├── CalorieDisplay.tsx   // A single number card (consumed / burned / net)
│   ├── CalorieTracker.tsx   // Computes and displays the totals
│   └── Form.tsx             // Add and edit form
├── data/
│   └── categories.ts        // Static categories: Comida (1), Ejercicio (2)
├── reducers/
│   └── activity-reducer.ts  // All state transitions
├── types/
│   └── index.ts             // Activity and Category types
├── App.tsx                  // Layout, reducer wiring, localStorage write
└── main.tsx                 // Entry point
```

## How it works

**State.** A single `useReducer` in `App.tsx` owns everything the UI needs. The shape is `{ activities, activeId }` and four actions cover the full feature set: `save-activity`, `set-activeId`, `delete-activity` and `restart-app`. There is no Redux, Zustand or Context. For an app this size the built-in primitives are enough and keep the code easy to follow.

**Persistence.** The reducer reads the activity list from `localStorage` when it initializes, and an effect in `App.tsx` writes back whenever the list changes. Only the activity list is persisted; `activeId` is treated as ephemeral UI state.

**Editing reuses the form.** When the pencil icon is clicked, the list dispatches `set-activeId` with that entry's id. The form watches `state.activeId`, copies the matching activity into local state, and on submit dispatches `save-activity`. The reducer notices that `activeId` is set and updates in place instead of appending a new entry.

**Totals are memoized.** `CalorieTracker` uses `useMemo` to compute totals by category id: id `1` adds to consumed, id `2` adds to burned, and the net is the difference between the two.

## Possible next steps

A few ideas for anyone who wants to extend it:

- Replace `localStorage` with a real backend or IndexedDB for multi-device sync.
- Add a daily target and a progress bar.
- Group entries by date instead of showing a single flat list.
- Add unit tests for the reducer (currently there is no test setup).
- Deploy a live preview (Vercel, Netlify or GitHub Pages).
