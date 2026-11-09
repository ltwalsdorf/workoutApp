# Tech Stack

A Progressive Web App (PWA) hosted on GitHub Pages and installed to the home screen of a Google Pixel 8 via Chrome. All data stays on the device. See [MISSION.md](MISSION.md) for what the app does.

## Overview

| Layer | Choice | Why |
|---|---|---|
| Language | **TypeScript** | Vite compiles it with zero config; catches data-model mistakes |
| UI framework | **React 19** | Stateful card/list screens are less code than vanilla DOM updates |
| Build tool | **Vite 6** | Dev server with hot reload; static build for Pages |
| PWA | **vite-plugin-pwa** | Manifest + service worker with a precache of Vite's hashed files, which is painful by hand |
| Storage | **localStorage**, one JSON document | Data is small; load once into React state, save on every change |
| Routing | **`location.hash` + `hashchange`** | A few screens; the Pixel's back button works; no router library |
| Reordering | **Up / down buttons** on exercise cards | Works on touch with no library |
| Styling | **Plain CSS** with CSS variables | No dependency |
| Icons | PNGs generated once and committed | No icon-generator dependency in the project |
| Testing | **Vitest** | Tests for the non-trivial logic: next routine template, last known values, import |
| Hosting | **GitHub Pages** | Free; repo is already public |
| CI/CD | **GitHub Actions** | Every push to `main` tests, builds and deploys |

Runtime dependencies: `react`, `react-dom`. Dev dependencies: `vite`, `@vitejs/plugin-react`, `vite-plugin-pwa`, `typescript`, `vitest`.

No backend, no database server, no accounts.

## Hosting and deploys

- **URL:** `https://ltwalsdorf.github.io/workoutApp/`
- Vite `base` is set to `/workoutApp/` so assets resolve under the repo path.
- `.github/workflows/deploy.yml` runs on push to `main`: install → test → build → publish `dist/` to Pages.
- Pages source is set to **GitHub Actions** in the repo settings.
- **Do not change the URL** once the app is in use. On-device data is tied to the origin, so a new host or domain starts empty (use export/import to move).

## Installing on the Pixel 8

1. Open the URL in Chrome.
2. Tap **⋮ → Install app** (or accept the install prompt).
3. It launches full-screen from the home screen and works offline.

## Updates

- The service worker uses `registerType: 'autoUpdate'`: after a deploy, the app picks up the new version on its next open.
- State is saved on every change, so an update reload never loses an in-progress workout.

## Data storage

- One JSON document under one localStorage key: `{ version, exercises, templates, categories, routine, workouts }`.
  - `exercises`: name, enabled metrics (reps / weight / time), last known values
  - `templates`: name, category, favorite flag, notes, ordered exercise targets (ranges)
  - `categories`: user-defined labels (Upper, Lower, Push, Pull, …)
  - `routine`: ordered template IDs, current position
  - `workouts`: date, source template, status (in progress / finished), ordered exercise entries with per-set actuals, check-off state, notes
- `version` is stored so a future format change can be migrated; no migration code until one is needed.
- Size limit: localStorage holds about 5 MB. A logged workout is around 2 KB, so that is years of history. If it ever gets close, move the same document to IndexedDB.
- `navigator.storage.persist()` is requested on first launch so Chrome is less likely to evict data.
- **Backup:** export the document to a JSON file and import it back. Import is validated before it replaces anything.

## Local development

- **Node 20.11** is installed locally. Vite 6 supports it; Vite 7+ would need Node 20.19 or newer.
- `npm run dev`: dev server with hot reload. Add `--host` to open it on the phone over the same Wi-Fi.
- `npm test`: Vitest
- `npm run build`: production build to `dist/`
- `npm run preview`: serve the production build locally (to test the service worker)

## Project layout

Start flat; split a file when it gets hard to read.

```
.github/workflows/deploy.yml   # test + build + deploy to GitHub Pages
public/                        # icons
src/
  main.tsx                     # entry, service worker registration
  App.tsx                      # hash routing between screens
  store.ts                     # load/save the JSON document, export/import
  logic.ts                     # next routine template, last known values
  logic.test.ts
  screens/                     # Home, Template, Routine, Workout, History
  styles.css
index.html
vite.config.ts
```
