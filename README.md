# Habib Data Hub — Mobile App (Flutter)

This is a complete Flutter project — `lib/`, `android/`, `ios/`, `test/`, and
all the platform configuration Flutter needs to build and run on both
Android and iOS.

## Run it

```bash
flutter pub get
flutter run --dart-define=API_BASE_URL=http://10.0.2.2:5000/api
```

## First-run notes

- **Placeholder branding**: launcher icons and the iOS launch image are
  generated placeholders (brand-blue background with a simple bolt mark) —
  swap them for your real logo before publishing. Easiest path: add your
  logo PNG to `assets/images/`, then use the `flutter_launcher_icons` and
  `flutter_native_splash` packages to regenerate every required size
  automatically instead of hand-editing `android/.../mipmap-*` and
  `ios/.../AppIcon.appiconset`.
- **`GeneratedPluginRegistrant` (iOS)**: `ios/Runner/GeneratedPluginRegistrant.{h,m}`
  are checked in as empty stubs so the Xcode project structure is complete.
  The moment you run `flutter pub get` (or `flutter build ios` / `flutter run`),
  Flutter regenerates these for real based on the plugins in `pubspec.yaml` —
  this is normal and expected, not a bug.
- **Android package / iOS bundle ID**: both are currently set to
  `com.habibdatahub.app`. Change this to your own reverse-domain identifier
  before publishing — see `docs/DEPLOYMENT_GUIDE.md` at the repo root for
  exactly where (Android: `android/app/build.gradle` → `applicationId`;
  iOS: Xcode → Signing & Capabilities, or the `PRODUCT_BUNDLE_IDENTIFIER`
  entries in `ios/Runner.xcodeproj/project.pbxproj`).
- **Signing**: `android/local.properties.example` shows the local file you
  need to create (never commit the real one). iOS signing is configured in
  Xcode itself (Signing & Capabilities tab) once you open
  `ios/Runner.xcworkspace` — always open the `.xcworkspace`, not
  `.xcodeproj`, once CocoaPods has run.
- **Firebase / push notifications**: commented-out in `lib/main.dart`,
  `ios/Runner/AppDelegate.swift`, and `android/app/build.gradle`. Run
  `flutterfire configure` to generate `lib/firebase_options.dart` and
  `google-services.json`/`GoogleService-Info.plist`, then uncomment those
  lines.

## Platform requirements

- Android: `minSdk 23` (Android 6.0+), `compileSdk 34`, Kotlin, Gradle 8.6
- iOS: deployment target `13.0`, Swift, CocoaPods (`cd ios && pod install`
  happens automatically on first `flutter run`, or run it manually)

See the repo-root `docs/SETUP_GUIDE.md` and `docs/DEPLOYMENT_GUIDE.md` for
full local setup and store-submission steps.
