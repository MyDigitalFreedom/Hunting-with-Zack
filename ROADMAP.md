# ROADMAP

The rough timing assumes evenings and weekends. Play's testing requirement is a fixed 14 days, so start phase 3's closed test as soon as phase 1 is stable.

## Phase 0: Repo and scaffold (about a weekend)
- [ ] Create the GitHub repo `hunting-with-zack`, public or private (see PROJECT → Open decisions)
- [ ] Add these docs, plus `legacy/hunting-with-zack.html`
- [ ] Scaffold the project: `npm create vite@latest . -- --template vanilla-ts`, then `npm i @capacitor/core @capacitor/cli @capacitor/android three`, then `npx cap init "Hunting with Zack" ca.strategicsystems.huntingwithzack --web-dir dist`, then `npx cap add android`
- [ ] Set `targetSdkVersion 36` and `compileSdkVersion 36` in `android/variables.gradle`
- [ ] Get the legacy page running inside the Android shell unchanged, to prove the pipeline works
- [ ] Add a GitHub Actions workflow that runs lint, tests and the build on every push

## Phase 1: Install link for Trent and Zack (2–4 weeks)
- [ ] Port the code into modules (ARCHITECTURE → Porting the prototype, steps 1–5)
- [ ] Hunt log on SQLite, plus JSON export and import
- [ ] Live weather through the Open-Meteo adapter, with an offline cache
- [ ] Bundle Three.js and the fonts; test in airplane mode
- [ ] "Find my WMU" using GPS
- [ ] App icon and splash screen (`@capacitor/assets`)
- [ ] Create a release keystore and **back it up in two places** (docs/RELEASE-ANDROID.md)
- [ ] A tag push builds a signed APK and attaches it to a GitHub Release
- [ ] Install on both phones; use it on a real hunt
- [ ] Register on Android Developer Verification before sideload checks reach Canada in 2027 (docs/RELEASE-ANDROID.md → Sideloading changes)

## Phase 2: Shared log and field features (2–4 weeks, can overlap phase 3)
- [ ] Sync API on Coolify (Fastify + Postgres), using a party invite code
- [ ] Two-way sync of trips and sightings; Trent and Zack see each other's sightings
- [ ] Legal-light notifications
- [ ] Compass wind check
- [ ] Keep the screen awake while a hunt is running
- [ ] "My sightings" chart that compares logged sightings with the model by hour

## Phase 3: Google Play (about 3–5 weeks, mostly waiting)
- [ ] Google Play Console account ($25 USD one-time, plus identity verification)
- [ ] Decide the store name and listing (PROJECT → Open decisions)
- [ ] Publish the privacy policy at a public URL (docs/PRIVACY.md → GitHub Pages)
- [ ] Data safety form, content rating questionnaire, target audience (adults)
- [ ] Upload an AAB and enrol in Play App Signing
- [ ] **Closed test: at least 12 testers opted in for 14 consecutive days.** Recruit friends, family and hunting contacts early
- [ ] Apply for production access; fix any review feedback
- [ ] Production rollout: start with a 20% staged rollout, then 100%

## Phase 4: iOS (3–6 weeks)
- [ ] Apple Developer Program ($99 USD a year)
- [ ] Get access to a Mac, or set up cloud macOS CI (docs/RELEASE-IOS.md)
- [ ] `npx cap add ios`; fix Safari and WebKit differences (pointer events, date handling, WebGL)
- [ ] TestFlight internal, then external with a public link
- [ ] App Store submission. Make sure it has enough native features to clear guideline 4.2, "minimum functionality"

## Every licence year (spring/summer)
- [ ] New regulations: update the season and rule data packs (docs/DATA.md)
- [ ] New harvest reports: add the year to the harvest data pack
- [ ] Bump `targetSdk` to whatever Play requires by its August deadline
- [ ] Release a new version before the archery openers (late August)
