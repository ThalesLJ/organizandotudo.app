<!--
Sync Impact Report
Version change: 0.1.0 -> 1.0.0
Modified principles:
- Established: I. Android Native Shell & Web Integration Boundary
- Established: II. Strict Typing, Kotlin Idioms, Naming Conventions & English Codebase
- Established: III. Internationalized and Preference-Aware UX
- Established: IV. Secure Session, Permissions and Android System Integration
- Established: V. Material 3 / Jetpack Compose UI System and Runtime Performance
- Established: VI. Simplicity & Focused Change
- Established: VII. Manual Quality Assurance
Added sections:
- Security Requirements
- Performance Requirements
- Spec Kit Workflow: Complex vs. Simple Features (Artifact Scalability Logic)
- Anti-Regression Checklist
- Post-Implementation Closure
- Governance
Templates requiring updates:
- .specify/templates/plan-template.md: aligned with Android/Kotlin stack, scaled artifact workflows, and manual QA gates
- .specify/templates/spec-template.md: aligned with simple/complex classification and English content rules
- .specify/templates/tasks-template.md: aligned with manual validation tasks and developer boundaries
Follow-up TODOs: none
-->
# Organizando Tudo App Constitution

## Core Principles

### I. Android Native Shell & Web Integration Boundary

The Android application (`com.ljsystems.organizandotudo`) serves as the dedicated mobile client
platform for Organizando Tudo. It hosts the Organizando Tudo web application through a hardened,
performance-tuned `WebView` shell while seamlessly integrating native Android capabilities (system bars
and insets management, light/dark status bar styling, download management via `DownloadManager`,
cookie synchronization via `CookieManager`, popup handling via `WebChromeClient`, and native Jetpack Compose /
Material 3 components where applicable).

The application endpoint MUST be loaded securely over HTTPS (`https://organizandotudo.thaleslj.com/` or
environment-configured URLs). Native-to-web and web-to-native bridges MUST remain strictly typed,
defensively verified, and minimal.

**Rationale**: The native shell guarantees an integrated mobile experience with OS-level capabilities
(downloads, notifications, theme awareness, native gestures) while leveraging the centralized
Organizando Tudo web ecosystem without duplicating business logic unnecessarily.

### II. Strict Typing, Kotlin Idioms, Naming Conventions & English Codebase

The general codebase language is English. All names for classes, interfaces, objects, Composables,
functions, methods, properties, variables, constants, packages, Gradle build scripts, and Spec Kit
artifacts MUST be written in English while remaining consistent with the application domain.

Kotlin strict type safety and nullability features MUST be respected:
- Avoid forced unwrapping (`!!`); use Kotlin safe-calls (`?.`), elvis operator (`?:`), `let`, or explicit contract checks.
- Untyped data structures (`Any`, raw maps) are prohibited unless explicit type narrowing or validation occurs before consumption.
- Asynchronous work MUST use Kotlin Coroutines (`kotlinx.coroutines`) and appropriate `CoroutineScope` / `Dispatchers` (`Dispatchers.Main` for UI, `Dispatchers.IO` for disk/network).

Naming conventions MUST follow Android / Kotlin standards:
- Classes, interfaces, objects, Composables, Activity, and Service classes MUST use PascalCase.
- Functions, methods, properties, parameters, and local variables MUST use camelCase.
- Immutable constants (top-level `const val`, companion object constants) and enum entries MUST use UPPER_SNAKE_CASE.
- Android XML resource files, drawables, layouts, and resource identifiers MUST use lower_snake_case (e.g., `ic_launcher_background.xml`, `colors.xml`, `strings.xml`, `themes.xml`).
- Package names MUST be all lowercase (e.g., `com.ljsystems.organizandotudo`).

**Rationale**: Strict adherence to Kotlin idioms and uniform naming prevents runtime regressions, crashes
(such as `NullPointerException`), and maintains clarity across the Android stack.

### III. Internationalized and Preference-Aware UX

All end-user visible text (dialogs, toasts, error messages, descriptions, notifications, content descriptions)
MUST be resolved through Android string resource catalogs (`res/values/strings.xml`, `res/values-pt/strings.xml`,
`res/values-es/strings.xml`). UI strings must NEVER be hardcoded in Kotlin/Compose code or XML layouts.

System and user preferences for language and theme (light/dark mode) MUST be respected as first-class behavior:
- Status bar, navigation bar, and WebView background colors MUST dynamically adjust according to the active system or app theme (e.g., light `#FDE1D4` / dark `#946A56`, background `#FFE3D5`) using `WindowInsetsController` / `systemBarsAppearance` across supported Android versions (API 29+ / Android 10+ up to Android 15 / API 35).
- Locale configurations MUST follow Android standards and fall back to default English (`res/values/strings.xml`) when a translation key is missing.

**Rationale**: Delivering a coherent, multilingual, and theme-adaptive experience is essential for a polished native mobile product.

### IV. Secure Session, Permissions and Android System Integration

Authentication tokens and session cookies inside WebView MUST be securely managed via Android `CookieManager`
(`setAcceptCookie(true)`, `setAcceptThirdPartyCookies` if required) with HttpOnly and HTTPS enforcement.
Tokens MUST NOT be logged or persisted in plain text or insecure SharedPreferences.

Android permissions in `AndroidManifest.xml` MUST follow the principle of least privilege. Dangerous or runtime
permissions MUST be properly requested at runtime on modern Android versions (API 29+).

File downloads, URI handling, and Intent dispatch MUST use secure Android platform mechanisms (`DownloadManager`,
scoped storage, `FileProvider`) avoiding world-readable file modes or exposed internal file paths.

Cleartext HTTP traffic is strictly prohibited; all remote communication and WebView navigation MUST use HTTPS.
Generic, empty `try/catch` blocks that silently swallow exceptions without proper logging or user-safe feedback are prohibited.

**Rationale**: Isolating credentials, securing WebView storage, and scoping Android permissions protect the user
against unauthorized access and security vulnerabilities.

### V. Material 3 / Jetpack Compose UI System and Runtime Performance

Native UI work MUST preserve the Material 3 design system, Compose theme tokens (`Theme.kt`, `Color.kt`, `Type.kt`),
and the brand visual identity (peach/brown color palette).

The main UI thread MUST never be blocked. File I/O, download processing, network operations, or heavy parsing
MUST be dispatched off the main thread.

WebView configurations MUST be optimized for smooth scrolling, fast rendering, and low memory usage. Avoid
redundant WebView re-creations or memory leaks across Activity lifecycles.

Third-party dependencies added to `gradle/libs.versions.toml` and `app/build.gradle.kts` MUST be strictly justified
by business value, bundle/APK size impact, and compatibility with the target SDK (API 35).

**Rationale**: A responsive, 60/120 fps interface that respects battery life and system resources delivers a premium user experience.

### VI. Simplicity & Focused Change

Implement the minimum change that satisfies the specification. Do not refactor unrelated modules, rename broad surfaces,
or introduce new abstractions unless the feature explicitly requires them.

Prefer existing Android/Compose primitives, AndroidX libraries, and established patterns over introducing redundant
custom implementations or unnecessary third-party libraries.

Avoid isolated adjustments in a single layer when changes touch manifest permissions, native bridge, theme configs,
or WebView settings — those changes MUST be coordinated end-to-end.

**Rationale**: Small, focused diffs reduce QA risk, make PR reviews clear, and prevent unintended regressions in app stability.

### VII. Manual Quality Assurance

Automated test suites are **not** mandatory in this repository. Quality assurance relies on manual validation
on devices/emulators and production QA unless the user explicitly requests automated tests.

Business logic, custom WebView bridges, and Compose components MUST still be written for future testability:
low coupling, high cohesion, pure functions where practical, and clean separation of concerns.

`tasks.md` MUST NOT plan or request automated tests (unit, espresso, compose test) unless the user explicitly asks
for them. Instead, `tasks.md` MUST include **manual validation steps** equivalent to the acceptance scenarios in `spec.md`
(e.g., verifying rendering on emulator/device, testing dark/light theme switching, testing download flow, verifying error handling).

**Rationale**: Reflects project QA practices while keeping code structurally testable if test automation is introduced later.

---

## Security Requirements

- Sensitive data (tokens, passwords, secrets, PII) MUST NOT appear in source code, logs, user-visible error messages,
  client-side storage, or unencrypted local preferences.
- Cleartext HTTP traffic is disabled; all network communication and WebView endpoints MUST use HTTPS.
- WebView settings MUST maintain safe browsing defaults (`allowFileAccess` only when strictly needed, avoid exposing
  arbitrary JavaScript interfaces, validate URLs before navigation).
- Android permissions in `AndroidManifest.xml` MUST be strictly justified and scoped.
- File downloads MUST use Android `DownloadManager` with appropriate destination paths (e.g., `DIRECTORY_DOWNLOADS`)
  and valid MIME types.
- Generic `try/catch` blocks that swallow errors without safe logging or user-safe messaging (e.g., Toast/Snackbar) are prohibited.

## Performance Requirements

- Main thread operations MUST NOT perform heavy I/O, network requests, or long-running computations.
- Android lifecycle events (`onCreate`, `onResume`, `onDestroy`, `onBackPressed`) MUST be handled cleanly without
  leaking views or holding invalid Activity context references.
- WebView client MUST cache static assets efficiently while ensuring fresh dynamic data is reflected.
- The Gradle build (`./gradlew assembleDebug` or `./gradlew build`) and linting (`./gradlew lint`) MUST pass cleanly without fatal errors.
- Additional third-party libraries in `gradle/libs.versions.toml` MUST be evaluated for APK size, dependency conflicts,
  and startup impact.

---

## Spec Kit Workflow: Complex vs. Simple Features

Specification-Driven Development (SDD) is the mandatory workflow for this project.
To balance thoroughness with efficiency, features are classified by complexity to scale
the number of generated design artifacts:

### 1. Feature Classification Criteria

| Classification                          | Criteria / Triggers                                                                                                                                                                                                                                                                                                                                                                                     | Required Artifacts                                                                                                                                                               |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Complex Feature** *(Standard Flow)*   | • New native Android modules, services, or major native capabilities<br>• New native database entities, Room schemas, or migrations<br>• Authentication, biometric security, or native session bridge changes<br>• External API integrations, native network clients, or complex WebView JS bridges<br>• High-risk security, background workers (`WorkManager`), or cross-cutting architectural changes | Full artifact pipeline:<br>1. `spec.md`<br>2. `research.md`<br>3. `plan.md`<br>4. `data-model.md`<br>5. `requirements.md`<br>6. `tasks.md`                                       |
| **Simple Feature** *(Streamlined Flow)* | • Pure UI/UX styling, theme adjustments, or cosmetic improvements<br>• Static screen additions or native dialogs<br>• Localization (`strings.xml`) dictionary / copy updates<br>• Minor bug fixes or enhancements to existing WebView settings, status bar handling, or simple UI components without new architecture                                                                                   | Streamlined artifact pipeline:<br>1. `spec.md`<br>2. `plan.md`<br>3. `tasks.md`<br>*(Omits `research.md`, `data-model.md`, and `requirements.md` to avoid unnecessary overhead)* |

### 2. Execution Order per Classification

#### A. Complex Features (Full Flow)
1. `spec.md` — Primary feature specification (`Status: Draft` initially).
2. `research.md` — Technical research, architecture decisions, and ambiguity resolution.
3. `plan.md` — Technical implementation plan and Constitution Check.
4. `data-model.md` — Entity schemas, state transitions, and data contracts.
5. `requirements.md` — Requirements validation checklist.
6. `tasks.md` — Actionable, dependency-ordered tasks with manual validation steps (generated ONLY after `requirements.md` is approved).

#### B. Simple Features (Streamlined Flow)
1. `spec.md` — Scoped feature specification with prioritized user stories, acceptance criteria, and edge cases (`Status: Draft` initially).
2. `plan.md` — Lightweight implementation plan with Constitution Check and affected files structure.
3. `tasks.md` — Actionable, dependency-ordered task list with manual validation steps (generated immediately after `plan.md`).

### 3. Workflow Rules for AI Agents & Developer Boundaries

- **Language**: Spec Kit artifacts MUST use English for technical names, entities, headings, and structure.
- **Forbidden Agent Tasks**:
  - `tasks.md` MUST NOT include Git operations (commits, pushes, branch switching, rebasing). Git operations are strictly developer responsibilities.
  - `tasks.md` MUST NOT include non-Android runtime commands (e.g., `dotnet`).
  - `tasks.md` MUST NOT include automated test generation/execution unless explicitly requested by the user.
- **Manual QA Tasks**: Every user story in `tasks.md` MUST contain explicit manual verification tasks mapping to the acceptance criteria in `spec.md`.

---

## Anti-Regression Checklist

Before completing an implementation or marking a feature done, verify:

- [ ] **Native Shell & WebView**: URL loading is secure (HTTPS); WebView settings, cookies, popups, and download handling operate reliably without memory leaks.
- [ ] **Auth & Session / Cookies**: Cookies properly synchronized via `CookieManager`; no sensitive tokens or secrets logged or stored insecurely.
- [ ] **Localization (i18n)**: All user-facing strings added to string resources (`res/values/strings.xml`, `res/values-pt/strings.xml`, `res/values-es/strings.xml`) with default English fallback; no hardcoded UI strings.
- [ ] **Theme & UI**: Consistent Material 3 / Jetpack Compose tokens used; status bar, navigation bar, and WebView background react properly to light/dark system themes across supported API levels.
- [ ] **Type Safety & Kotlin Idioms**: Strict null safety enforced without `!!`; explicit coroutine scopes and dispatchers used; no unvalidated `Any` types.
- [ ] **Error Handling & User Feedback**: No silent `try/catch` swallows; user-safe feedback (Toasts, dialogs, Snackbars) provided for failure scenarios.
- [ ] **Performance & Lifecycle**: Heavy operations executed off the main thread; Activity lifecycle transitions handled without leaking context.
- [ ] **Spec Kit Compliance & Closure**: All tasks verified through manual validation steps; feature status in `spec.md` updated from `Draft` to `Done`.

---

## Post-Implementation Closure

After the AI agent completes all tasks in `tasks.md` for a feature, the agent MUST
automatically update the `Status` property in that feature's `spec.md` from `Draft` to `Done`.

This update is mandatory as the final systemic step of the AI implementation cycle and is
independent of subsequent manual QA performed by the developer in staging or production.

---

## Governance

This constitution is the authoritative source for Organizando Tudo App
architectural and development principles. Specifications, plans, tasks, code
changes, PR reviews, and documentation MUST be checked against these guidelines
before an implementation is considered complete.

The principles described here represent practices established in the
project codebase. Any new feature, architectural change, or code review MUST
adhere to these guidelines to avoid introducing architectural regressions,
crashes, domain inconsistencies, sensitive data exposure, or security failures.

Significant changes to these practices MUST be justified in versioning,
including the reason for the change, affected principles or sections, version
impact, and required updates to Spec Kit templates or project documentation.
Versioning follows semantic rules:

- MAJOR: incompatible governance changes or removal/redefinition of core principles.
- MINOR: new principles, new mandatory sections, or material expansion of guidance.
- PATCH: clarifications, wording improvements, and non-semantic corrections.

Compliance review MUST verify preservation of the native shell boundary,
secure cookie handling, i18n consistency, theme preferences, security rules, the
scaled Spec Kit flow (simple vs. complex), manual quality, and anti-regression checks.

**Version**: 1.0.0 | **Ratified**: 2026-08-18 | **Last Amended**: 2026-08-18
