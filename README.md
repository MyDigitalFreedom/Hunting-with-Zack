# Hunting with Zack

An Alberta big game hunt planner for Android (iOS later). It started as a single-page web tool and is becoming a real phone app.

Pick a WMU and species, and the app shows:

- **Activity:** when animals are likely moving, and your shot chance for the day or outing.
- **Conditions:** weather, the moon, wind and stand placement, and how much hunting pressure to expect.
- **Rules:** season dates and the area's rules.
- **History:** the past four years of harvest results.
- **Shot placement:** a 3D model with the vitals shown.
- **Processing:** a guide for cutting your own meat or filling out a butcher's cut sheet.
- **Practice and records:** a scope practice game, and a hunt log that records every animal you see.

> **Not a substitute for the regulations.** Season dates and WMU rules come from the Alberta Hunting Regulations for the current licence year. Always check the official [Alberta Hunting Regulations](https://www.albertaregulations.ca/huntingregs/) and the [My Wild Alberta](https://mywildalberta.ca/) updates before you hunt.

## Install (Android, testers)

1. On your Android phone, open the **[latest release](../../releases/latest)** and download `hunting-with-zack-<version>.apk`.
2. When Android asks, allow your browser to install unknown apps.
3. Open the APK and tap **Install**.

To get updates automatically, add this repo to [Obtainium](https://github.com/ImranR98/Obtainium). It checks GitHub Releases for new versions.

Play Store and App Store listings are planned. See [ROADMAP.md](ROADMAP.md).

## Project docs

| File | What's in it |
|---|---|
| [PROJECT.md](PROJECT.md) | What the app is, who it's for, its features, and the decisions made so far |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Tech stack, code layout, data flow, offline use and sync |
| [ROADMAP.md](ROADMAP.md) | The phases from a git repo, to an install link, to Play Store, to App Store |
| [docs/DATA.md](docs/DATA.md) | Where each dataset comes from and how to update it each year |
| [docs/RELEASE-ANDROID.md](docs/RELEASE-ANDROID.md) | Signing, GitHub Releases APK, and Play Store submission |
| [docs/RELEASE-IOS.md](docs/RELEASE-IOS.md) | Apple developer setup, TestFlight, and App Store submission |
| [docs/PRIVACY.md](docs/PRIVACY.md) | Draft privacy policy, which both stores require |
| [CLAUDE.md](CLAUDE.md) | Working notes for Claude Code in this repo |
| `legacy/hunting-with-zack.html` | The original single-file web version, which is the porting reference |

## Develop

```bash
npm install
npm run dev            # web dev server with hot reload
npm run build          # production web build -> dist/
npx cap sync android   # copy the build into the Android project
npx cap run android    # build and run on a connected phone or emulator
```

You need Node 20 LTS or newer, plus Android Studio with the Android SDK (API 36) and JDK 21.

## Licence

To be decided. See PROJECT.md → Open decisions.
