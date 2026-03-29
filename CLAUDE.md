# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**St Michael's Golf Club — JDP Coach App**

A single-page coaching management system for the Junior Development Program (JDP). Multiple coaches can view and manage junior golfer data simultaneously with real-time synchronisation via Supabase.

## Architecture

This is a **monolithic single-file application** — all HTML, CSS, and JavaScript live in `index.html`. There is no build system, no package manager, and no compilation step. The file is deployed and served as-is.

### Data Layer

- Backend: Supabase (PostgreSQL), accessed directly from the browser via the JS client library loaded from a CDN
- All data is stored as key-value pairs in the `jdp_data` table, namespaced with `stm_*`
- Two core functions handle all persistence: `dbGet(key)` and `dbSet(key, value)`
- Real-time sync uses Supabase's `channel()` subscription — changes made by one coach are reflected in all open windows automatically

### Key Data Structures

- `allProfiles` — array of junior golfer objects (the central entity)
- `coachList` — array of coach names
- `benchLogs` — performance test log entries
- `curricData` — curriculum structure by squad/level
- `livePlannerData` — term planner data keyed by squad

### Configuration Objects (hardcoded in JS)

- `SQUADS` — 5 squads: Bronze, Silver, Gold, Elite, Private (key, label, icon, color)
- `BENCH_TESTS` — golf tests (Long Drive, Chipping, Putting, etc.) and physical tests (Vertical Jump, Shuttle Run, etc.)
- `CHS_THRESHOLDS` — Club Head Speed benchmark bands, separated by gender (M/F), 10 levels from Beginning → Elite
- `calcLevel()` / `calcCHSLevel()` / `getBand()` — convert raw metrics to 1–10 level bands

### UI Pattern

- Tab-based navigation dispatched by `showTab(tabName)`
- Each tab has a `render*()` function that rebuilds its UI from current data
- Modal overlays for Quick Log (`#ql-overlay`) and parent reports (`#report-modal`)
- Contenteditable cells in the Term Planner are saved on blur

### Initialisation

`init()` runs on page load: loads all data from Supabase, builds the full UI, and subscribes to real-time changes.

## Development Workflow

No build step required. Open `index.html` directly in a browser, or serve it with any static file server:

```bash
# Python (if available)
python -m http.server 8080

# Node (if available)
npx serve .
```

There are no tests, no linter, and no CI configuration.

## Deployment

Replace the single `index.html` file on your static host (GitHub Pages, Netlify, etc.). No build artifacts.

## Important Notes

- The Supabase URL and publishable key are embedded in the HTML — this is intentional for this app's use case (browser-only, no server)
- The club identifier constant `CLUB = 'stm'` namespaces all Supabase keys for St Michael's; changing it would switch to a different club's data
- All state is server-side (Supabase); there is no local state management library
- When editing, keep HTML/CSS/JS in `index.html` — do not introduce a build system unless explicitly asked
