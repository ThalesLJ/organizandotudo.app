---
description: "Task list template for feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md (if Complex), data-model.md (if Complex), requirements.md (if Complex)

**Tests & QA**: Automated tests are **OPTIONAL** (do not include automated tests unless explicitly requested by the user). Every User Story MUST include explicit **Manual Validation Tasks** matching the acceptance criteria in `spec.md`.

**Organization**: Tasks are grouped by user story to enable independent implementation and manual verification of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Android App**: `app/src/main/java/com/ljsystems/organizandotudo/`, `app/src/main/res/`, `app/build.gradle.kts`
- **Gradle Version Catalog**: `gradle/libs.versions.toml`

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit-tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Manual validation steps for each acceptance scenario

  Rules:
  - DO NOT include Git operations (commits, branch changes, pushes).
  - DO NOT include automated tests unless explicitly requested.
  - DO include explicit manual validation steps for every user story.
  - DO include the final Post-Implementation Closure task (spec.md Status -> Done).
  ============================================================================
-->

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Dependencies and project initialization

- [ ] T001 [P] Update `gradle/libs.versions.toml` or `app/build.gradle.kts` if new dependencies/permissions are required
- [ ] T002 [P] Add string keys to `app/src/main/res/values/strings.xml` (and `pt`/`es` if applicable)

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T003 Configure native component, theme token, or WebView client settings
- [ ] T004 Setup error handling or permission handling structure

**Checkpoint**: Foundation ready - user story implementation can now begin

---

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to manually verify this story works on emulator/device]

### Implementation for User Story 1

- [ ] T005 [P] [US1] Create or update native component in `app/src/main/java/com/ljsystems/organizandotudo/[file].kt`
- [ ] T006 [US1] Integrate with `MainActivity.kt` or Compose theme
- [ ] T007 [US1] Add error handling and user feedback (Toast / Snackbar / Dialog)

### Manual Validation for User Story 1

- [ ] T008 [US1] Manual validation: [Action] on emulator/device and verify [Expected outcome per spec.md acceptance criteria]
- [ ] T009 [US1] Manual validation: Verify light/dark theme behavior and status bar appearance

**Checkpoint**: At this point, User Story 1 should be fully functional and manually validated

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to manually verify this story works on emulator/device]

### Implementation for User Story 2

- [ ] T010 [P] [US2] Implement feature in `app/src/main/java/com/ljsystems/organizandotudo/[file].kt`
- [ ] T011 [US2] Add resource styling in `app/src/main/res/values/[file].xml`

### Manual Validation for User Story 2

- [ ] T012 [US2] Manual validation: [Action] on emulator/device and verify [Expected outcome per spec.md]

**Checkpoint**: User Stories 1 AND 2 work and have passed manual verification

---

## Phase N: Polish & Post-Implementation Closure

**Purpose**: Build verification and Spec Kit status closure

- [ ] T013 Verify project build and linting (`./gradlew assembleDebug` and `./gradlew lint`)
- [ ] T014 Update feature specification status in `specs/[###-feature]/spec.md` from `Draft` to `Done`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
- **Polish & Closure (Final Phase)**: Depends on all user stories being implemented and manually validated

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2)
- **User Story 2 (P2)**: Can start after Foundational (Phase 2)

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1 (Implementation + Manual Validation)
4. **STOP and VALIDATE**: Verify User Story 1 on emulator/device

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Validate manually → MVP
3. Add User Story 2 → Validate manually
4. Verify build & linting → Update `spec.md` status to `Done`
