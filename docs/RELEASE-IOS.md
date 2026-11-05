# iOS release guide (phase 4)

## Requirements
- **Apple Developer Program:** $99 USD a year, as an individual or an organization. An organization needs a D-U-N-S number, and its name shows as the seller.
- **A Mac with Xcode** to build and sign. Options:
  - Your own Mac. A used Apple Silicon Mac mini is the cheapest reliable route.
  - Cloud CI with macOS: GitHub Actions `macos-latest` runners, Codemagic, or Ionic Appflow.
  - A rented cloud Mac, such as MacinCloud, for occasional builds.
- An iPhone for testing (borrowed is fine), or the Simulator.

## Setup
```bash
npm i @capacitor/ios
npx cap add ios
npx cap sync ios
npx cap open ios      # opens Xcode
```
- Bundle ID: `ca.strategicsystems.huntingwithzack`, the same as Android.
- In Xcode, go to Signing & Capabilities and set the team. Turn on Push only if you need it later; local notifications don't need it.
- Info.plist usage strings (required, or the app is rejected):
  - `NSLocationWhenInUseUsageDescription`: "Finds the WMU you're standing in and the sun times for your position."
  - `NSMotionUsageDescription`, if the compass or orientation feature is used.

## WebKit differences to test
- Pointer events on the date and time tapes, and on the scope game's drag and tap.
- Three.js and WebGL performance on older iPhones. Lower the pixel ratio if needed.
- `<input type=date>` and font rendering.
- Safe areas (notch and home bar): use `env(safe-area-inset-*)`.

## TestFlight (the iOS "install link")
- **Internal:** up to 100 people on your App Store Connect team. No review.
- **External:** up to 10,000 testers through a **public link**. The first build of each version needs a short beta review.
- Builds expire after 90 days.

## App Store review notes
- **Guideline 4.2, minimum functionality:** Apple rejects apps that are "just a website in a wrapper". Highlight the native features: offline data, GPS WMU lookup, local notifications, the compass, and the on-device hunt log.
- **Privacy:** App Privacy "nutrition label" answers that match docs/PRIVACY.md. The privacy policy URL is required.
- **Sign in with Apple:** required only if the app offers third-party sign-in (Google, Facebook). An invite code or email login avoids it.
- **Age rating:** the questionnaire covers realistic violence, for the scope game and the hunting content. Expect a 12+ to 17+ rating.
- **Account deletion:** if phase 2 accounts exist, the app must offer a way to delete the account from inside the app.
