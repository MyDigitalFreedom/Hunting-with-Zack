# Android release guide

## 1. Signing key (do this once, and do it carefully)

The same key must sign every APK you share. If you lose it, phones that installed the sideloaded app can't update it; they'd have to uninstall it and lose any on-device data.

```bash
keytool -genkeypair -v -keystore hwz-release.jks -alias hwz \
  -keyalg RSA -keysize 4096 -validity 10000
```

- **Never commit** `*.jks` or `keystore.properties`. Add both to `.gitignore`.
- Back it up in two places, such as a password manager attachment and an encrypted USB drive. Store the passwords separately.
- For CI, store the keystore as a base64 GitHub Actions secret (`ANDROID_KEYSTORE_B64`), along with `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_ALIAS` and `ANDROID_KEY_PASSWORD`.

In `android/app/build.gradle`:

```gradle
def ksProps = new Properties()
def ksFile = rootProject.file("keystore.properties")
if (ksFile.exists()) ksProps.load(new FileInputStream(ksFile))
android {
  signingConfigs {
    release {
      storeFile file(ksProps['storeFile'] ?: System.getenv('KS_FILE') ?: 'none')
      storePassword ksProps['storePassword'] ?: System.getenv('KS_PASS')
      keyAlias ksProps['keyAlias'] ?: System.getenv('KS_ALIAS')
      keyPassword ksProps['keyPassword'] ?: System.getenv('KS_KEY_PASS')
    }
  }
  buildTypes { release { signingConfig signingConfigs.release; minifyEnabled false } }
}
```

## 2. Versioning

- `versionName` uses semver (`1.2.0`). `versionCode` is a whole number that **must go up with every release**. One easy scheme is `major*10000 + minor*100 + patch`.
- Tag releases as `v1.2.0`. CI reads the tag and sets both values.

## 3. Phase 1: GitHub Releases install link

Workflow: `.github/workflows/release.yml`, triggered when a `v*` tag is pushed.

```yaml
name: release
on: { push: { tags: ['v*'] } }
jobs:
  apk:
    runs-on: ubuntu-latest
    permissions: { contents: write }
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - uses: actions/setup-java@v4
        with: { distribution: temurin, java-version: 21 }
      - run: npm ci && npm test && npm run build && npx cap sync android
      - name: Decode keystore
        run: echo "${{ secrets.ANDROID_KEYSTORE_B64 }}" | base64 -d > $RUNNER_TEMP/release.jks
      - name: Build APK + AAB
        working-directory: android
        env:
          KS_FILE: ${{ runner.temp }}/release.jks
          KS_PASS: ${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
          KS_ALIAS: ${{ secrets.ANDROID_KEY_ALIAS }}
          KS_KEY_PASS: ${{ secrets.ANDROID_KEY_PASSWORD }}
        run: ./gradlew assembleRelease bundleRelease
      - name: Rename
        run: cp android/app/build/outputs/apk/release/app-release.apk hunting-with-zack-${GITHUB_REF_NAME}.apk
      - uses: softprops/action-gh-release@v2
        with:
          files: |
            hunting-with-zack-*.apk
            android/app/build/outputs/bundle/release/app-release.aab
          generate_release_notes: true
```

**Share this link:** `https://github.com/<you>/hunting-with-zack/releases/latest`

- In a **public repo**, anyone can download the APK.
- In a **private repo**, release assets need a GitHub login with access. To share with people outside GitHub, do one of these:
  - Keep a small public "releases-only" repo.
  - Host the APK on your own server through Coolify.
  - Use Firebase App Distribution, which sends email invites to testers.
- **Obtainium:** testers add the repo URL and get update prompts for every new release.

### Sideloading changes (Android developer verification)

Google is requiring developer verification even for apps installed outside Play.

- **Since Sept 30, 2026:** enforcement started in Brazil, Indonesia, Singapore and Thailand.
- **2027:** it expands worldwide, including Canada.
- **Unverified apps** will still install, but only through a deliberate "advanced flow" with a 24-hour wait, or through ADB.
- **Plan:** before the global date, either register a **limited-distribution (hobbyist) account**, which is free, needs no ID, and allows up to 20 devices, or complete **full verification** (free once you have a Play Console account in phase 3). After that, your APK installs normally.
- Recheck the [Android developer verification](https://developer.android.com/developer-verification) page when you start phase 1, since the dates have moved before.

## 4. Phase 3: Google Play

### Account
- Create a Google Play Console developer account ($25 USD one-time). It can be **personal** or an **organization**. An organization account needs a D-U-N-S number, but StrategicSystems could qualify.
- **Personal accounts created after Nov 13, 2023 must run a closed test with at least 12 opted-in testers for 14 days in a row** before they can apply for production. If testers drop below 12, the 14-day count resets. Organization accounts are exempt.

### Before the first upload
- `targetSdk 36` (Android 16). It has been required for new apps and updates since Aug 31, 2026.
- Release as an **AAB**, not an APK. Enrol in **Play App Signing**. Google holds the app signing key, and your keystore becomes the upload key.
  - Play-signed builds use Google's signing key. Phones with the sideloaded APK must uninstall it once before installing from Play, so export the hunt log first.
  - Alternatively, during Play App Signing setup, upload your existing key so both builds share the same signature.
- App ID `ca.strategicsystems.huntingwithzack` is permanent once uploaded.

### Store listing checklist
- [ ] App name (30 chars), short description (80), full description (4000)
- [ ] Icon 512×512 PNG; feature graphic 1024×500
- [ ] At least 2 phone screenshots (activity, map, 3D, hunt log, weather)
- [ ] Category: Sports, or Maps & Navigation. Tags: hunting, outdoors
- [ ] Contact email, and the privacy policy URL (docs/PRIVACY.md hosted on GitHub Pages)

### Policy forms
- **Data safety:**
  - Declare location (approximate and precise, optional, used for app functionality, not shared).
  - Declare app activity / user content (the hunt log). If phase 2 sync is on, it's collected and encrypted in transit, and users can request deletion.
  - No ads, no analytics. If you later add crash reporting, declare it.
- **Content rating (IARC):**
  - Answer honestly about the hunting content and the **scope practice game**, which shows realistic shooting of animals.
  - Expect a Teen / PEGI 12–16 type rating. Set the target audience to 18+ so it isn't treated as a kids' app.
- **Ads:** none. **Government apps:** no; add an in-app disclaimer that the app isn't affiliated with the Government of Alberta.
- **Health/financial/sensitive:** no.

### Tracks
1. **Internal testing:** you and Zack (up to 100). Installs within minutes, no review.
2. **Closed testing:** at least 12 testers for 14 days. Use a Google Group or an email list, and send testers the opt-in link.
3. **Production:** apply in Play Console. Answer the questions about your testing honestly; they ask what you learned and changed.

### Ongoing
- Every August, Play raises the `targetSdk` requirement. Plan an update each summer anyway, for the new regulations.
