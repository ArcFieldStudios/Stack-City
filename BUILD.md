# Building the City Stack Android app

This repo wraps `city-stack_4.html` (a self-contained canvas game) as a native
Android app using [Capacitor](https://capacitorjs.com/). The web game itself
lives at the repo root and is mirrored into `www/index.html`, which is what
gets bundled into the native app — after editing `city-stack_4.html`, copy it
to `www/index.html` before rebuilding.

- App name: **City Stack**
- Package / application ID: `com.arcfieldstudios.citystack`
- Orientation: locked to portrait (matches the game's UI)

## Prerequisites

- Node.js + npm
- JDK 17+
- Android SDK (platform 36, build-tools matching Android Gradle Plugin 8.13.0)
  with `ANDROID_HOME`/`local.properties` configured — install via Android
  Studio or `sdkmanager`.

## One-time setup

```bash
npm install
```

## Rebuilding the web assets into the native project

```bash
cp city-stack_4.html www/index.html
npx cap sync android
```

## Release signing

A release build needs `android/keystore.properties` (gitignored, never
commit it) pointing at your upload keystore. Copy the template and fill in
your real values:

```bash
cp android/keystore.properties.example android/keystore.properties
```

If you don't already have an upload keystore, generate one:

```bash
keytool -genkeypair -v -storetype PKCS12 \
  -keystore keystore/citystack-upload.jks \
  -alias citystack -keyalg RSA -keysize 2048 -validity 10000
```

Keep that `.jks` file and its passwords somewhere safe outside the repo —
losing it means you can't publish updates to the same Play Store listing.

## Building the AAB

```bash
cd android
./gradlew bundleRelease
```

Output: `android/app/build/outputs/bundle/release/app-release.aab`

## Notes on this environment

Building here requires network access to `dl.google.com` (Android Gradle
Plugin + AndroidX artifacts) and, for the initial SDK install, to Google's
SDK repository. Some sandboxed/cloud dev environments block that domain by
policy — if `./gradlew` fails with 403s against `dl.google.com`, that's why;
build locally or from an environment with unrestricted network access
instead.
