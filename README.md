# Reference Browser (Custom Browser Project)

> **Important Note for AI Assistant / Developer (Project Overview & Roadmap)**:
> This workspace contains the **Mozilla Reference Browser** (`org.mozilla.reference.browser`), built with **Mozilla GeckoView** and **Android Components**.
> Read the section below for full project context, existing features, diagnosed issues, planned customizations, and build instructions.

---

## 📌 Project Context & Custom Roadmap (PocketDev Workspace)

### 1. Project Identification
- **App Name:** Reference Browser (Target: Custom Android Web Browser)
- **Package:** `org.mozilla.reference.browser`
- **Core Engine:** **GeckoView** (same Gecko engine powering Mozilla Firefox for Android)
- **Framework:** Mozilla Android Components + Jetpack Compose
- **Status:** Complete, fully-functional ready-made web browser source code.

### 2. Existing Features (Pehle se mojud features)
- **Engine:** GeckoView (HTML5, modern ES6+ JS, CSS3, WebAssembly, WebGL, WebAuthn).
- **Navigation:** AwesomeBar (URL address bar + integrated search suggestions + SSL indicators).
- **Tabs:** Normal multi-tab tray + Private Browsing (Incognito) mode with custom styling.
- **Extensions:** Full support for Firefox WebExtensions (Add-ons manager, AMO overrides).
- **Sync & Accounts:** Firefox Accounts (FxA) sync for open tabs, history, passwords, and QR pairing.
- **Media & Utilities:** Picture-in-Picture (PiP) video, background media playback controls, Download Manager, Reader View, Find-in-page, Autofill service, Android Custom Tabs.

---

### 3. Diagnosed Issue: Google AI Studio Slowness on Gecko/Firefox
- **Observation:** When using Google AI Studio (`aistudio.google.com/apps/...`), the app takes ~1+ minute "thinking/launching" before streaming code, whereas on Brave (Chromium) it is instantaneous.
- **Root Causes:**
  1. **Chromium Optimization:** Google AI Studio is heavily optimized for Chromium/V8 WebAssembly and streaming APIs.
  2. **Enhanced Tracking Protection (ETP):** Firefox/Gecko's ETP blocks or throttles background telemetry, cross-origin web workers, and Server-Sent Events (SSE) streaming connections used by Google AI Studio.
  3. **Client-side Container/Wasm:** AI Studio runs a client-side container (`Launching...`) that initialises slower on GeckoView than on V8.
  4. **User-Agent Discrimination:** Google serves different/fallback JavaScript bundles to non-Chromium user agents.

---

### 4. Planned Roadmap & Tasks
1. **Performance Fixes for Google AI Studio & Heavy Web Apps:**
   - **User-Agent Spoofing:** Configure GeckoView (`GeckoRuntimeSettings` / `GeckoSession`) to send a modern Chrome/Chromium User-Agent for Google domains (or globally), forcing Google to serve its fast-path assets.
   - **Tracking Protection Whitelist:** Relax/whitelist ETP rules for trusted developer platforms (like `aistudio.google.com`, `firebase`, etc.) so streaming/websockets are never blocked.
   - **GeckoView Performance Flags:** Optimize WebAssembly JIT, caching, and hardware acceleration in `EngineProvider.kt` / `BrowserApplication.kt`.
2. **App Branding & Customizations:**
   - Change App Name in `app/src/main/res/values/strings.xml` (`app_name`).
   - Replace launcher icons and splash screens in `app/src/main/res/mipmap-*` and `res/drawable-*`.
   - Customize default search engines (Google, DuckDuckGo, Bing, etc.).
   - Customize Home Page / New Tab Page UI and default bookmarks/shortcuts.
   - Theme and colors adjustments (`res/values/colors.xml`, `style.xml`).
3. **Build & Test Debug APK:**
   - Compile debug APK using the PocketDev build script (see commands below).

---

### 5. Build Instructions (PocketDev Environment Rules)
> **CRITICAL:** Never run plain `gradle build` or start a Gradle daemon. Build debug APK only.
- **Check existing APK:** Check `app/build/outputs/apk/debug/` first.
- **First Build (Dependencies Resolution):**
  ```bash
  timeout 15m gradle -Dorg.gradle.jvmargs= --no-daemon --max-workers=2 --init-script /root/.gradle/init.d/pocketdev-android.gradle -Pandroid.aapt2FromMavenOverride=/root/android-sdk/build-tools/35.0.0/aapt2 assembleDebug --console=plain --stacktrace
  ```
- **Cached Fast Path (When APK/dependencies exist):**
  ```bash
  timeout 5m gradle -Dorg.gradle.jvmargs= --no-daemon --max-workers=2 --offline --init-script /root/.gradle/init.d/pocketdev-android.gradle -Pandroid.aapt2FromMavenOverride=/root/android-sdk/build-tools/35.0.0/aapt2 assembleDebug --console=plain --stacktrace
  ```
- If cached build fails due to missing dependency, retry once online with `timeout 15m` without `--offline`.

---

# Reference Browser Original Documentation

[![Task Status](https://firefox-ci-tc.services.mozilla.com/api/github/v1/repository/mozilla-mobile/reference-browser/main/badge.svg)](https://firefox-ci-tc.services.mozilla.com/tasks/index/mobile.v2.reference-browser.nightly.latest) [![chat.mozilla.org](https://img.shields.io/badge/chat-on%20matrix-51bb9c)](https://chat.mozilla.org/#/room/#android-components:mozilla.org)

A web browser reference implementation using [Mozilla Android Components](https://mozac.org).

*The Reference Browser is not a product intended to ship to end users. Instead it is a Technology Preview for many new mobile components that multiple teams at Mozilla are currently working on*

It includes the Mozilla Web Platform via GeckoView, a new modern Firefox Accounts and Cloud Sync implementation and the new "Glean" telemetry library. All these components will be foundational for Mozilla's existing and upcoming Android products.

The Reference Browser can also be a starting point for your own new browser-like applications. It depends heavily on the [Android Components](https://hg.mozilla.org/mozilla-central/file/tip/mobile/android/android-components) project where most of the actual implementation lives. That project also includes many smaller sample applications.

**Will Reference Browser move to mozilla-central?**

Reference Browser is meant to be a reference implementation of the Android Components project. Having it live outside of the mozilla source-tree lets us:

1. See how core browsing APIs work for browser-based projects outside of Firefox.
2. Have a developer playground to try experimental APIs in a safe way that involves a full build, signing, and publishing process.
3. Validates that our [maven](https://nightly.maven.mozilla.org) [repositories](https://maven.mozilla.org) and Google Play publishing infrastructure is still functioning - if we no longer receive maven artifacts or Google Play builds are not working, we would know that there is something systematically wrong with these processes.

# Getting Involved

We encourage you to participate in this open source project. We love pull requests, bug reports, ideas, (security) code reviews or any kind of positive contribution.

Before you attempt to make a contribution please read the [Community Participation Guidelines](https://www.mozilla.org/en-US/about/governance/policies/participation/).

* [View current Issues](https://github.com/mozilla-mobile/reference-browser/issues) or [View current Pull Requests](https://github.com/mozilla-mobile/reference-browser/pulls).

* IRC: [#android-components (irc.mozilla.org)](https://wiki.mozilla.org/IRC) | [view logs](https://mozilla.logbot.info/android-components/)

* Subscribe to our mailing list [android-components@](https://lists.mozilla.org/listinfo/android-components) to keep up to date ([Archives](https://lists.mozilla.org/pipermail/android-components/)).


# Test Channel on Google Play Store

To get the Reference Browser on your device, follow these two steps:

1) Visit https://groups.google.com/forum/#!forum/reference-browser and join the Google Group
2) Visit https://play.google.com/apps/testing/org.mozilla.reference.browser on your device to join the test program and to install the app

Make sure you use the same Google Account for both steps.

# Download Nightly Builds Directly

Signed Nightly builds can be downloaded from:

* [⬇️ ARM64/Aarch64 devices (64 bit; Android 8+)](https://firefox-ci-tc.services.mozilla.com/api/index/v1/task/mobile.v2.reference-browser.nightly.latest.arm64-v8a/artifacts/public/target.arm64-v8a.apk)
* [⬇️ ARM devices (32 bit; Android 8+)](https://firefox-ci-tc.services.mozilla.com/api/index/v1/task/mobile.v2.reference-browser.nightly.latest.armeabi-v7a/artifacts/public/target.armeabi-v7a.apk)
* [⬇️ x86_64  devices (64 bit; Android 8+)](https://firefox-ci-tc.services.mozilla.com/api/index/v1/task/mobile.v2.reference-browser.nightly.latest.x86_64/artifacts/public/target.x86_64.apk)

> Please note that these builds do not auto-update, you will have to keep up to date manually.

The latest Nightly build task can be found [here](https://firefox-ci-tc.services.mozilla.com/tasks/index/mobile.v2.reference-browser.nightly.latest).

# Getting Involved

We encourage you to participate in this open source project. We love pull requests, bug reports, ideas, (security) code reviews or any kind of positive contribution.

Before you attempt to make a contribution please read the [Community Participation Guidelines](https://www.mozilla.org/en-US/about/governance/policies/participation/).

* [View current Issues](https://github.com/mozilla-mobile/reference-browser/issues) or [View current Pull Requests](https://github.com/mozilla-mobile/reference-browser/pulls).

* [List of good first issues](https://github.com/mozilla-mobile/reference-browser/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) (**New contributors start here!**) and [List of "help wanted" issues](https://github.com/mozilla-mobile/reference-browser/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22).

* IRC: [#android-components (irc.mozilla.org)](https://wiki.mozilla.org/IRC) | [view logs](https://mozilla.logbot.info/android-components/)

* Subscribe to our mailing list [android-components@](https://lists.mozilla.org/listinfo/android-components) to keep up to date ([Archives](https://lists.mozilla.org/pipermail/android-components/)).

# Local Development

You might be interested in building this project against local versions of some of the dependencies. Depending on which dependencies you're building against, there are couple of paths.

## Auto-publication workflow

This is the most streamlined workflow which fully automates dependency publication. It currently supports [android-components](https://github.com/mozilla-mobile/android-components/) and [application-services](https://github.com/mozilla/application-services) dependencies.

In a `local.properties` file in root of the `reference-browser` checkout, specify relative paths to a repository you need (or both):
```
# Local workflow
autoPublish.android-components.dir=../android-components
autoPublish.application-services.dir=../application-services
```

That's it! Next build of `reference-browser` will be against your local versions of these repositories. Simply make changes in `android-components` or `application-services`, press Play in `reference-browser` and those changes will be picked-up.

See a [demo of this workflow](https://www.youtube.com/watch?v=qZKlBzVvQGc) in action. Video mentions `Fenix`, but it works in exactly the same with with `reference-browser`.

## Dependency substitutions for [GeckoView](https://hg.mozilla.org/mozilla-central)

GeckoView currently can be configured via a dependency substitution.

In a `local.properties` file in root of the `reference-browser` checkout, specify GeckoView's path via `dependencySubstitutions.geckoviewTopsrcdir=/path/to/mozilla-central` (and, optionally, `dependencySubstitutions.geckoviewTopobjdir=/path/to/topobjdir`). See [Bug 1533465](https://bugzilla.mozilla.org/show_bug.cgi?id=1533465).

This assumes that you have built, packaged, and published your local GeckoView -- but don't worry, the dependency substitution script has the latest instructions for doing that.

Do not forget to run a Gradle sync in Android Studio after changing `local.properties`. If you specified any substitutions (e.g. GeckoView), they will be reflected in the modules list, and you'll be able to modify them from a single Android Studio window. For auto-publication workflow, use seperate Android Studio windows.

# License

    This Source Code Form is subject to the terms of the Mozilla Public
    License, v. 2.0. If a copy of the MPL was not distributed with this
    file, You can obtain one at http://mozilla.org/MPL/2.0/
