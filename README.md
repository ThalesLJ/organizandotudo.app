# Organizando Tudo App

Organizando Tudo App is the official native Android mobile client for the Organizando Tudo ecosystem, built with Kotlin, Jetpack Compose, and Material 3. It encapsulates the web experience within a hardware-accelerated, performance-tuned Android WebView container seamlessly integrated with native Android system services, including download management via `DownloadManager`, cookie synchronization via `CookieManager`, dynamic status and navigation bar theming, and responsive mobile UX.

## Tech Stack

- **Framework**: Android SDK (Target SDK 35, Min SDK 29 / Android 10+)
- **Language**: [Kotlin](https://kotlinlang.org/) 2.0.21
- **Build System**: Android Gradle Plugin (AGP) 8.10.1 & Gradle
- **UI Toolkit**: [Jetpack Compose](https://developer.android.com/jetpack/compose) (BOM 2024.09.00) & [Material 3](https://m3.material.io/)
- **Core Android Libraries**: AndroidX Core KTX, Lifecycle Runtime KTX, Activity Compose
- **Web Engine**: Android WebView with `WebChromeClient`, `WebViewClient`, and `CookieManager`
- **System Integration**: Android `DownloadManager` and `WindowInsetsController`

## Architecture

The mobile application serves as a dedicated native Android host container for Organizando Tudo:

```text
Android OS -> MainActivity (ComponentActivity) -> WebView Client & WebChromeClient -> CookieManager / DownloadManager / System Insets -> Secure Web Application (HTTPS)
```

### Architectural Highlights

- **Native System Insets & Theming**: Dynamically customizes system status bar and navigation bar appearances based on system light/dark mode using `WindowInsetsController` (e.g., light `#FDE1D4` / dark `#946A56`, background `#FFE3D5`).
- **Integrated Download Manager**: Intercepts web download events and transparently delegates file transfers to Android `DownloadManager` with full cookie and user-agent forwarding.
- **Hardware-Accelerated WebView**: Configured with DOM storage, hardware acceleration, popup window support via `WebChromeClient`, and error interceptors.
- **Cookie & Session Synchronization**: Preserves authenticated sessions using `CookieManager` over secure HTTPS connections.

## Project Structure

```text
organizandotudo.app/
├── .specify/                       # Spec Kit memory, configurations, and templates
│   └── memory/
│       └── constitution.md         # Project constitution and architectural principles
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/ljsystems/organizandotudo/
│   │   │   │   ├── ui/theme/       # Jetpack Compose theme (Color.kt, Theme.kt, Type.kt)
│   │   │   │   └── MainActivity.kt # Android Activity hosting WebView with native integration
│   │   │   ├── res/                # Android visual assets, mipmaps, strings, and XML configs
│   │   │   └── AndroidManifest.xml # Android application manifest and permissions
│   │   ├── build.gradle.kts        # Application-level Gradle build script
│   │   └── proguard-rules.pro      # ProGuard/R8 release minification rules
├── gradle/
│   ├── wrapper/                    # Gradle wrapper binaries and properties
│   └── libs.versions.toml          # Centralized version catalog for dependencies
├── build.gradle.kts                # Root project Gradle build script
├── gradle.properties               # JVM and Gradle runtime configuration
├── gradlew                         # Gradle wrapper shell script (Linux/macOS)
├── gradlew.bat                     # Gradle wrapper batch script (Windows)
├── settings.gradle.kts             # Gradle settings and plugin management
└── README.md                       # Project documentation
```

## Getting Started

### Prerequisites

- **IDE**: [Android Studio](https://developer.android.com/studio) (Ladybug / Meerkat or newer)
- **JDK**: Java Development Kit (JDK) 17 or higher
- **Android SDK**: Android API 35 SDK Platform and Build Tools

### Running from Android Studio

1. Open Android Studio and choose **Open Project**.
2. Select the `organizandotudo.app` directory.
3. Allow Gradle to synchronize dependencies.
4. Select a connected physical Android device or an Android Virtual Device (AVD) running API 29+.
5. Click **Run 'app'** (or press **Shift + F10**).

### Running from Command Line

1. Build the debug APK:
   ```bash
   ./gradlew assembleDebug
   ```
   *(On Windows: `gradlew.bat assembleDebug`)*

2. Install on a connected device/emulator via ADB:
   ```bash
   adb install -r app/build/outputs/apk/debug/app-debug.apk
   ```

## Available Scripts

| Action | Command | Description |
|---|---|---|
| **Build Debug APK** | `./gradlew assembleDebug` | Compiles debug APK with logging enabled |
| **Build Release APK** | `./gradlew assembleRelease` | Compiles optimized release APK |
| **Build App Bundle (AAB)** | `./gradlew bundleRelease` | Generates Android App Bundle for Google Play Store |
| **Run Lint** | `./gradlew lint` | Performs static code and resource analysis |
| **Run Unit Tests** | `./gradlew test` | Executes local unit tests |
| **Clean Project** | `./gradlew clean` | Deletes build outputs and temporary cache |

## Deployment

The Android application is configured for distribution via **Google Play Store** (via Android App Bundle `.aab`) and direct APK sideloading.

- **Application ID**: `com.ljsystems.organizandotudo`
- **Target SDK**: API 35 (Android 15) | **Min SDK**: API 29 (Android 10)
- **Distribution Artifacts**:
  - **Android App Bundle (`.aab`)**: Generated using `./gradlew bundleRelease` for Google Play Console release tracks.
  - **Signed Release APK (`.apk`)**: Generated using `./gradlew assembleRelease` and signed with release keystores.
- **Minification & Optimization**: Configured with ProGuard/R8 rules in `app/proguard-rules.pro`.

## SDD (Microsoft Speckit)

This project is developed using **Specification-Driven Development (SDD)** with Microsoft Spec Kit. All features, architecture modifications, and bug fixes must originate from structured specifications before code changes are applied.

- **Agent Guidance & Setup**: [`AGENTS.md`](./AGENTS.md)
- **Project Constitution**: [`.specify/memory/constitution.md`](./.specify/memory/constitution.md)

Main Spec Kit commands:

```text
/speckit.constitution - Establish or update project principles
/speckit.specify      - Create a baseline feature specification
/speckit.plan         - Create technical implementation plan
/speckit.tasks        - Generate actionable task breakdown
/speckit.implement    - Execute implementation tasks
```
