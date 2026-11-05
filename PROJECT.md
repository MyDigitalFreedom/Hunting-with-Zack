# PROJECT: Hunting with Zack

## Purpose

This is a phone-first planner for hunting deer, elk and moose in Alberta. It answers four questions for a chosen WMU, species, date and time:

1. **Is it open, and what are the rules?** Season dates, Sunday closures and mandatory CWD submission.
2. **When will animals be moving?** An activity model that factors in the rut, the weather, the moon, hunting pressure and wind.
3. **What are my real odds?** A shot-opportunity percentage based on the WMU's harvest success and your hunting style, experience and outing length.
4. **What do I do after the shot?** Shot placement, field care, and a guide to cutting your own meat or ordering from a butcher.

It also keeps a **hunt log** of real sightings. Over time, that log is meant to replace the model's assumptions with what you've actually seen.

## Users

- **Primary:** Trent and Zack, two experienced hunters in the Stony Plain area. They mostly hunt from a truck, with some sitting and walking.
- **Secondary (phase 3 onward):** a small circle of friends and family, then anyone hunting in Alberta through the stores.

## Where it stands (v0, the web version)

The single HTML file in `legacy/` is the working prototype. It has these features:

| Area | Feature | Notes |
|---|---|---|
| Header | Species picker (whitetail, mule deer, elk, moose) | Drives every section |
| Area | Clickable Alberta WMU map | Colour modes: region, harvest, success, density, rules, open. Pan, zoom and pinch |
| Area | Selection card | Rule chips (Sunday closed, CWD mandatory) and a season summary |
| Hunt log | Start/stop a hunt, tap a species to log a sighting with its time | Synced in the prototype through claude.ai storage, which must be replaced |
| 01 Activity | Date and time drag tapes, weather type, licence, hunter (Trent=experienced, Zack=pro), style, experience, outing length | Translucent deer filled by shot % |
| 01 Activity | Readouts | Sunrise, sunset, legal hours, rut, season, weather, moon, pressure, wind |
| 02 Harvest | By-year table, 2022–2025, all four species | Parsed from the Alberta harvest PDFs |
| 03 Weather | 8-day bars for the nearest of 55 towns, moon, wind and stand placement, storm notes | **The prototype's weather is a static snapshot. The app needs live data** |
| 04 Shot placement | Rotatable Three.js model per species, vitals, translucent shoulder blades | |
| 05 After the shot | Do-it-yourself path (quartering, deboning, yield estimate) or butcher cut sheet | |
| 06 Scope practice | Drag to aim, tap to shoot, five stalks | |
| 07 Story | Story for the next hunt | Personal content; keep it out of public builds or make it a user note |

## What the app adds over the prototype

Each item here either needs the phone or fixes a shortcut the prototype took.

- **Live weather.** Fetch forecasts for the selected WMU's town and cache them for offline use (see ARCHITECTURE).
- **"Which WMU am I in?"** Use GPS and a point-in-polygon check against the WMU boundaries. It works offline.
- **Offline-first.** Bundle all data and libraries (Three.js and fonts included) so it works with no signal in the field.
- **Hunt log stored on the phone** in a local database, with export and import. Optional sync between Trent and Zack in phase 2.
- **Legal light alerts.** Local notifications before legal light starts and ends at your current position.
- **Compass wind check.** Use the phone's heading to show where the wind is relative to where you're facing.
- **Keep the screen awake** during an active hunt.
- **Yearly data packs.** Ship new regulations and harvest data as app updates, or later as downloadable data.

## Non-goals (for now)

- Turn-by-turn navigation or full topographic maps. Use onX or Gaia for those; we might add a deep link to them.
- Social feeds or public sharing of locations.
- Buying licences or drawing applications. Link to AlbertaRELM instead.
- Provinces other than Alberta.

## Key decisions

| # | Decision | Why |
|---|---|---|
| D1 | **Capacitor** wraps a Vite web app | The prototype is already HTML, JS and Three.js. One codebase builds Android now and iOS later, with native plugins for GPS, notifications and storage |
| D2 | **Vanilla TypeScript modules**, no UI framework at first | Port the prototype's code module by module. Consider a framework later only if the UI grows |
| D3 | **Offline-first local storage** (SQLite through a Capacitor plugin) | Hunting areas have poor signal, and the log must never lose a sighting |
| D4 | **Optional self-hosted sync API** (Postgres + Docker Compose on Coolify) | Reuses Trent's standard stack. Phase 2 only, so the app works without it |
| D5 | **Weather behind an adapter interface** | Lets us swap Open-Meteo, Environment Canada (MSC GeoMet) or others without touching the UI |
| D6 | App ID `ca.strategicsystems.huntingwithzack` | Reverse-domain of Trent's business domain. **The app ID can never change after the first Play upload** |
| D7 | Android first: GitHub Releases APK → Play Store. Then iOS: TestFlight → App Store | Matches the plan. Play's testing requirement and iOS's need for a Mac set the pace |

## Open decisions

- [ ] **Public name for the stores.** "Hunting with Zack" is personal. Keep it, or pick a store name such as "Alberta Hunt Planner" and keep "Hunting with Zack" as the private build?
- [ ] **Public or private repo.** A public repo makes the install link easy but exposes the code. A private repo needs another way to host the APK.
- [ ] **Licence** (if public): MIT, GPL, or all rights reserved.
- [ ] **Free or paid in the stores.** Open-Meteo's free tier is for non-commercial use only, so a paid app or ads means a paid weather plan or switching to Environment Canada data.
- [ ] **Whether to keep the story section** in public builds.
- [ ] **Sync accounts**: invite code, email magic link, or Google sign-in. Note that Google sign-in on iOS triggers Apple's Sign in with Apple requirement.

## Success criteria

- Phase 1: Trent and Zack both have the APK installed, use it offline at Westmans, and lose no hunt log entries.
- Phase 3: The app is live on Google Play with an approved content rating and data safety form.
- Phase 4: The app is live on the App Store.
