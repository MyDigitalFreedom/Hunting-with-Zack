# Project Overview

Hunting with Zack is a phone-first Alberta big game hunt planner (deer, elk, moose), built as a **Capacitor + Vite + TypeScript** app. Android first, iOS later.

This file tells Claude Code *how* to work on this project. The *what* lives in:
- `PROJECT.md` — purpose, users, features, decisions, success criteria (this is the project spec)
- `ARCHITECTURE.md` — stack, repo layout, data flow
- `ROADMAP.md` — phases and what's next
- `docs/DATA.md`, `docs/PRIVACY.md`, `docs/RELEASE-ANDROID.md`, `docs/RELEASE-IOS.md`

---

# Core Rule

Do exactly what is asked. Nothing more, nothing less. If something is unclear, ask before starting.

---

# Development Rules

**Rule 1: Always read first**
Before taking any action, read `PROJECT.md`, `ARCHITECTURE.md` and `PROGRESS.md`. If `PROGRESS.md` doesn't exist, create it.

**Rule 2: Define before you build**
Before writing code for a new feature, check it is described in `PROJECT.md` / `ROADMAP.md`. If it isn't, or the task changes a decision in the Key decisions table, update the doc first, show me, and wait for approval. No code before approval.

**Rule 3: Look before you create**
Look at existing files before creating new ones. Reuse existing modules and patterns.

**Rule 4: Plan big changes**
If a task touches more than 3 files, changes stored data (SQLite schema or data pack format), adds a device permission, or changes sync/auth, list the plan and wait for approval.

**Rule 5: Test before you respond**
Never say "done" if the code is untested. See Testing.

**Rule 6: Stop when stuck**
If the same fix fails twice, stop. Explain what you tried, what you think is wrong, and the options. Never "fix" an error by deleting code, disabling a check, adding `any`, or skipping a test.

**Rule 7: Keep a progress log**
At the end of every task, update `PROGRESS.md`: what was done, what's next, decisions made and why, known issues. Lasting decisions also go in the Key decisions table in `PROJECT.md`.

---

# Agent Team

The main session is the **lead**. It talks to me, plans, and hands work to four agents in `.claude/agents/`:

| Agent | Job | Can edit code? |
|---|---|---|
| `researcher` | Investigates the codebase, docs and options. Writes findings to `docs/research/` | No |
| `coder` | Implements the approved plan | Yes |
| `reviewer` | Reviews changes for bugs, security, rule breaks and messy code. APPROVE / CHANGES REQUESTED | No |
| `tester` | Runs tests and the app, verifies the change. PASS / FAIL | No |

**Workflow:** lead confirms task → researcher (if anything is unknown) → lead plans, waits for approval → coder → reviewer + tester in parallel → if either fails, back to coder (max 2 rounds, then stop and explain) → on APPROVE + PASS, lead updates `PROGRESS.md` and reports.

**Rules for the lead:** agents don't share memory — pass them the task, plan, file paths and coder's notes. Only the coder edits app code. Parallel only for independent work. For a one-line fix or a question, skip the team. Tell me which agent is working in one short line.

---

# How to Respond

Explain in plain English for someone with no coding background. Be concise.

Every response includes:
- **What I just did**
- **What you need to do** — step by step, assume they've never seen it before
- **Why** — one sentence
- **Next step** — one clear action
- **Errors** — what went wrong and exactly how to fix it

For external tools (Android Studio, Play Console, GitHub, Coolify, a phone over USB): say exactly where to click, what each setting does in one sentence, and before any command, what it does and whether it changes or deletes anything.

---

# Rules Specific to This App

- **The porting reference is `legacy/hunting-with-zack.html`.** When porting a feature, match its behaviour and numbers. Add a golden test before refactoring any model function.
- **Keep `src/model/` pure:** no DOM, no network, no storage. It must stay unit-testable.
- **Offline-first:** no runtime CDN or network dependency except weather and sync. Both go through adapters and fall back to the cache. Fonts, Three.js and data packs are bundled.
- **Time zone:** Alberta is UTC−6 all year. Use the fixed `TZ` offset, not `Intl` or the device time zone, for sun, legal light and season calculations.
- **Regulations data is safety-relevant.** Never "fix" season dates from memory; change them only from the regulations PDF, and note the page.
- **Privacy:** no background location tracking, analytics or ads without updating `docs/PRIVACY.md` and the store data-safety notes.
- **Units:** °C, metres/kilometres. Currency (if ever needed) in CAD, stored as integer cents.
- **App ID** `ca.strategicsystems.huntingwithzack` can never change after the first Play upload.

---

# Design

Senior UI designer mindset: premium, modern, clear hierarchy, subtle animation. No emoji icons. No generic gradients.

- **Phone-first, field-first.** Large tap targets, readable in sunlight, usable with gloves and one hand.
- Use the theme tokens (CSS custom properties, light and dark) ported from the prototype. Never hard-code colours or one-off sizes.
- Accessibility: real `<button>`/`<label>` elements, keyboard navigable, visible focus, contrast at least 4.5:1, alt text.
- Every data view has a loading, empty, error **and offline** state.
- One icon set only.

---

# Tech Stack

See `ARCHITECTURE.md` for the full table. In short: Capacitor, Vite, vanilla TypeScript (strict, no UI framework), bundled `three`, `@fontsource/*` fonts, `@capacitor-community/sqlite` (IndexedDB fallback in browser dev), vitest. Phase 2 sync API: Node (Fastify) + Postgres, Docker Compose on Coolify.

- Pin versions in `package.json`; don't upgrade major versions without asking.
- **Ask before adding any package.** Say what it does, why, and whether something installed already does it.

---

# Commands

- `npm run dev` — web dev server
- `npm test` — vitest (model golden values)
- `npm run validate-data` — schema-check `public/data/*.json`
- `npm run build && npx cap sync android` — build and update the native Android project

---

# How to Write Code

- Simple, readable code — clarity over cleverness. One change at a time.
- Don't change code unrelated to the task. Spot a problem elsewhere? Mention it, don't fix it.
- Clear names (`getActiveHunt()`, not `getData()`). No `any`. No commented-out code.
- Files over ~250 lines should be split — ask first if unsure how.
- Don't create new top-level folders without asking.
- Never show raw errors to the user; show a clear, friendly message.

---

# Data & Storage

- Local SQLite holds hunts, sightings, settings and cached forecasts. A schema change needs a versioned migration — never edit one that has shipped.
- The hunt log must never lose a sighting: write it immediately, use transactions for multi-table writes.
- Data packs in `public/data/` follow `docs/DATA.md` and must pass `npm run validate-data`.
- Never delete or reset stored data without asking and saying exactly what will be lost.
- **Phase 2 sync API (`server/`)** follows the standard server rules: Postgres with migrations, every query scoped to the logged-in user, never trust an ID from the client, rate-limit auth routes, `/health` endpoint, back up before production migrations.

---

# Secrets & Safety

- Never commit keystores, `keystore.properties`, `.env` or API secrets.
- New config key → add it (without the value) to `.env.example` and tell me where to get the value.
- Ask before deleting or renaming any important file.

---

# Releases

- Every release: bump `versionCode`, update `CHANGELOG.md`, follow `docs/RELEASE-ANDROID.md` (or `RELEASE-IOS.md`).

---

# Git Rules

- Never work directly on `main` — one branch per feature (`feature/short-name`, `fix/short-name`).
- Commit after each working step with a clear message. Run tests and build before committing.
- Check `git status` before committing so no secrets or junk get in.
- Never force-push, rewrite history, or merge to `main` without asking.

---

# Testing

Before marking any task done:
- `npm test`, `npm run validate-data` and `npm run build` pass
- `npm run dev` runs with no console errors
- The feature works in the browser at phone width **and**, for anything touching device features (GPS, notifications, storage, keep-awake), on an Android device or emulator
- It works with the network off (except live weather/sync, which must fall back to cache)
- Existing features still work
- Model changes: golden values still match the legacy prototype

If something couldn't be tested, say so plainly.

---

# Scope

Only build what is in `PROJECT.md` / `ROADMAP.md`. Ideas go in `PROGRESS.md` under "Ideas" — don't build them.
