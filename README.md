# CubeRank · SJTU 魔方周赛

A zero-install web app for running weekly speedcubing meetups: enter results, watch the leaderboard re-rank live, and export the final standings.

Pure front-end (HTML + CSS + vanilla JS). No backend, no build step, works offline. Data is stored in your browser.

> UI language: Simplified Chinese. Dark and light themes.

## Highlights

- **Live ranking.** Enter a competitor's solves and the leaderboard re-sorts instantly, with animated rank changes (▲/▼ badges, "new" tag).
- **Fast data entry.** `Enter` jumps to the next field. Accepts `8.62`, `61.59`, `1:01.59` and `1:1.59`. Extra decimals are truncated to two places. Names autocomplete from the player library.
- **Penalties.** One-click `+2` and `DNF` per solve.
- **Rules handled for you.** Ao5 (drop best and worst), Ao3 for 3-attempt events, tie handling, DNF logic, and single-first ranking for blindfolded events.
- **17 events.** From 3×3 to Multi-BLD, each with its own icon and page. Add events mid-competition and drag to reorder.
- **Edit and delete.** Pick an existing name and its solves load back into the form; change or delete them. The leaderboard has ✏️ / 🗑 buttons on every row.
- **Player profiles.** Cross-competition stats, medals, personal bests, and a per-event history chart (time distribution histogram or line chart).
- **Records.** Club single and average records per event, computed from all competitions.
- **PK.** Head-to-head comparison of any two players across events.
- **Excel in, Excel/PDF out.** Import past results from `.xlsx`; export per event or all at once.
- **Themes.** Dark and light, remembered between visits.

## Quick start

No installation needed.

1. Download or clone the project.
2. Open `index.html` in a modern browser (Chrome, Edge, Firefox, Safari).

Or serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Typical workflow

1. **Create a competition** on the home page: name, date, and the events to run. Or choose **Excel import** to load past results.
2. **Open an event** and enter each competitor's solves. Press `Enter` to move between fields; the last `Enter` submits.
3. Watch the **live leaderboard**. Click a name for that player's per-event breakdown.
4. Click **End competition** to lock results and unlock exports. You can reopen it if you need to correct something.
5. **Export** Excel or PDF per event, or everything at once.

## Ranking rules

| Event type | Average | Ranking |
|---|---|---|
| 5-attempt (3×3, 2×2, 4×4, 5×5, OH, …) | **Ao5**: drop best and worst, average the middle three | Ao5 first, then best single |
| 3-attempt (6×6, 7×7) | **Ao3**: plain mean of three, nothing dropped | Ao3 first, then best single |
| 3-attempt blindfolded (3BLD, 4BLD, 5BLD) | Ao3 | **Best single first**, then average |

Penalties and edge cases:

- `+2` adds two seconds and displays as e.g. `8.62+`.
- `DNF` counts as the slowest solve of the round. Two or more DNFs in an Ao5 make the average DNF. In 3-attempt events, any DNF makes the average DNF.
- Ties share a rank.
- Valid times are greater than 0 and at most 3600 s. Seconds in `m:ss` must be below 60.

## Excel import format

- One worksheet per event. The **sheet name must exactly match the event name** shown in the app (e.g. `三阶`, `二阶`, `三盲`).
- **No header row.** Each row is: player name, followed by all of that player's solve times.
- No need to pre-compute best, average, or rank; the app does it.
- If a file fails to parse, the app reports exactly which sheet or row is wrong.

## Export

- **Excel:** one file with a sheet per event, or one event at a time.
- **PDF:** one file with a page per event, or one event at a time.

Exports are also a convenient backup, and they can be re-imported using the format above.

## Project structure

```
.
├── index.html          # Entry page (loads CSS, libs and scripts)
├── css/
│   └── style.css       # Theme variables, layout, animations
├── js/
│   ├── data.js         # Data layer: events, storage, parsing, stats, ranking, records
│   ├── import.js       # Excel import and validation
│   ├── export.js       # Excel / PDF export
│   └── app.js          # UI: hash router, pages, modals, charts, leaderboard animation
└── libs/
    ├── xlsx.full.min.js   # SheetJS (Excel read/write)
    └── jspdf.umd.min.js   # jsPDF (PDF generation)
```

### Routes (hash-based)

| Route | Page |
|---|---|
| `#/` | Home: create or import a competition, competition list |
| `#/comp/:id` | Competition overview, end and export |
| `#/comp/:id/event/:eventId` | Event page: entry form and live leaderboard |
| `#/players` | Player list and rankings |
| `#/player/:name` | Player profile |
| `#/records` | Club records |
| `#/pk/:left/:right` | Head-to-head comparison |

## Data and privacy

- All data lives in the browser's `localStorage` (key `sjtu-cube-weekly-v1`); theme is stored under `sjtu-cube-weekly-theme`.
- Nothing is sent to a server.
- Clearing site data, switching browsers or devices, or using private mode will lose or hide your data. Export regularly and keep the Excel files as backups.

## Browser support

Any modern browser with ES6+ and `localStorage`. Designed for desktop; layouts are responsive.

## Tech stack

Vanilla JavaScript · CSS custom properties · [SheetJS](https://sheetjs.com/) · [jsPDF](https://github.com/parallax/jsPDF)
