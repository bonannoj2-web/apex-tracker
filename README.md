# Apex Squad Tracker

A passcode-gated squad performance tracker for Apex Legends, built for a private squad ("Three Stooges") to log matches, track pro-style scoring, and see stats sync live across teammates' devices.

**Live site:** https://apex-squad-k7x2m9-fieldr1.vercel.app (passcode required, not indexed by search engines)

## What it does

- Log each match's squad placement and per-player stats (damage, kills, knockdowns, assists, revives, respawns, survival time, plus optional damage-taken/deaths).
- Pro-style ALGS placement points (12 for 1st down to 0 for 16th–20th).
- Live leaderboard, session MVP, and a lowest-scorer "roast" with a themed one-liner.
- Player Legend Breakdown, Legend Pick Rate, Map Breakdown, Squad Comp Breakdown, Performance-over-time charts.
- Player Stat Lines (K/D, KA/D, Dealt÷Taken, kill participation) once Deaths / Damage Taken are entered — these never affect scoring.
- Personal Bests, full Match History, and browsable Past Sessions with an All-Time career view.
- Live map-rotation auto-fill on the match form (via the mozambiquehe.re API), with a manual override that's preserved if you've already picked a map.
- Export/import JSON backups.
- Data is shared across the whole squad in real time (10s poll, with 3-way merge for concurrent edits) via a passcode-protected Supabase backend — nothing is stored only in one person's browser.

## Stack

- **Frontend:** a single self-contained vanilla JS file (`main.js`) that renders the whole app — no build step, no framework.
- **Backend:** Supabase (Postgres). All access goes through three passcode-checked RPC functions (`squad_load`, `squad_version`, `squad_save`) — the table itself has row-level security enabled and is not otherwise reachable. Passcodes are bcrypt-hashed; 5 wrong tries locks a squad out for 5 minutes.
- **Hosting:** Vercel, deployed as a static site with security headers and a CSP set in `vercel.json`. The page is marked `noindex` and sends `X-Robots-Tag`, so it won't show up in search results — the passcode is what actually protects the data.

## Files

- `index.html` — loads `app.css` and `main.js`.
- `main.js` — the entire app: rendering, cloud sync, scoring, all panels.
- `app.css` — styling.
- `vercel.json` — security headers and Content-Security-Policy.
- `robots.txt` — disallows all crawling.

The production deployment on Vercel currently serves `main.js` split into several small chunks (an artifact of how it was uploaded through Claude's deployment tooling, which has a per-file size limit) — that's purely a deployment detail. This repo's `index.html` loads it as a single `<script src="/main.js">`, which is the simpler and recommended way to deploy it (e.g. via Vercel's GitHub integration or CLI, which don't have that same constraint).

## Configuration

The Supabase URL and anon key, and the mozambiquehe.re API key, are embedded directly in `main.js`. This is intentional and safe for the Supabase key — it's the public "anon" key, and all it can do is call the three passcode-gated RPC functions above (the underlying table has RLS enabled and is not otherwise reachable, and Postgres does the passcode checking, not the client). The mozambiquehe.re key is a free-tier public API key for map-rotation data.

## Local development

Just open `index.html` in a browser, or serve the folder with any static file server. There's no build step.
