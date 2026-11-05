# PROGRESS

## Done
- 2026-11-04: Replaced CLAUDE.md (was a generic Next.js template copied from Family Tree) with a combined version: general working rules + this app's Capacitor/offline rules.

- 2026-11-04: Updated the four agents (`.claude/agents/`) to this app's stack: offline, legacy parity, UTC−6, regulation-source, on-device and phase-2-only server checks.

- 2026-11-04: Fixed git. The `.git` folder was empty (no history lost); removed it and ran `git init -b main`. First commit on `main` holds the docs, agents and legacy prototype (owner approved this one commit on `main`).

## Next
- Start Phase 1 per `ROADMAP.md` on a `feature/` branch.

## Decisions
- `PROJECT.md` is the project spec (no separate `project_specs.md`) — avoids two documents saying the same thing.
- Time zone: fixed UTC−6 all year for sun, legal light and season maths (confirmed by project owner 2026-11-04).

- Dropped `npm run lint` from agent checks — not in CLAUDE.md and no app code yet. Add a lint script and check when coding starts.

## Known issues / TODOs
- No lint script yet (see decision above).

## Ideas
- None yet.
