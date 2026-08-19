<!--
Sync Impact Report
Version change: 1.2.0 -> 1.3.0
Modified principles:
- Added: VI. Simplicity & Focused Change
- Added: VII. Manual Quality Assurance
- Refined: I. Thin Frontend and BFF Boundary
- Refined: II. Strict Typing, Naming Conventions & English Codebase
- Refined: III. Internationalized and Preference-Aware UX
- Refined: IV. Secure Session and Server-Side Integration
- Refined: V. Consistent UI System and Runtime Performance
Added sections:
- Spec Kit Workflow: Complex vs. Simple Features (Artifact Scalability Logic)
- Anti-Regression Checklist
Modified sections:
- Mandatory Spec Kit Flow -> Spec Kit Workflow: Complex vs. Simple Features
- Development Workflow and Quality Gates -> Anti-Regression Checklist & Spec Kit Workflow
- Governance
Templates requiring updates:
- .specify/templates/plan-template.md: aligned with scaled artifact workflows and manual QA gates
- .specify/templates/spec-template.md: aligned with simple/complex classification and English content rules
- .specify/templates/tasks-template.md: aligned with manual validation tasks and developer boundaries
Follow-up TODOs: none
-->
# Organizando Tudo Web Constitution

## Core Principles

### I. Thin Frontend and BFF Boundary

The Next.js application MUST remain both the user interface layer and the
Backend for Frontend (BFF) boundary. Browser-executed code MUST call only internal
Next.js API routes for application data. External API communication, token
forwarding, request normalization, and sensitive decisions MUST remain inside
server-side route handlers or shared server-side utilities.

The expected data flow is:

```text
Client -> Next.js BFF -> External API -> Next.js BFF -> Client
```

**Rationale**: The implemented architecture protects credentials, centralizes
integration behavior, and keeps UI components focused on rendering and user
interaction without coupling them to external service details.

### II. Strict Typing, Naming Conventions & English Codebase

The general codebase language is English. All names for classes, properties,
methods, functions, variables, business rules, entities, DTOs, modules,
controllers, providers, services, repositories, schemas, contracts, and Spec Kit
artifacts MUST be written in English while remaining consistent with the application domain.

TypeScript `strict` mode MUST remain enabled. The JavaScript `var` keyword and the
TypeScript `any` type are **prohibited** (along with uncontracted objects,
`Record<string, any>`, and type suppressions without real handling). `unknown` MAY be
used only when explicit narrowing or validation happens before consumption.

Naming conventions MUST follow idiomatic standards:
- Classes, components, decorators, modules, controllers, providers, services,
  repositories, entities, DTOs, schemas, types, interfaces, and enums MUST use PascalCase.
- Methods, functions, properties, parameters, and local variables MUST use camelCase.
- Immutable global constants and environment variables MUST use UPPER_SNAKE_CASE.
- Files, folders, routes, and technical artifacts SHOULD use kebab-case.
- Interfaces MUST NOT be required to start with `I`; names MUST represent the contract clearly.

Input parsing and request validation MUST be explicit. Zod schemas SHOULD be
used in frontend and route handler flows that are already standardized around them.
In backend/service code, DTOs, pipes, validators, and typed contracts SHOULD be
prioritized to keep validation consistent and coupling low.

**Rationale**: Explicit types and uniform naming prevent runtime regressions, keep
API boundaries safe, and maintain readability across the entire stack.

### III. Internationalized and Preference-Aware UX

All end-user visible text (labels, messages, validation text, notifications, page copy)
MUST be resolved through the centralized localization catalog when it belongs to shared
application flows or persisted UX. Supported languages are English (`en`), Portuguese (`pt`),
and Spanish (`es`). New language keys MUST preserve the same structure across all locales
and fall back to English when a translation key is missing or invalid.

User preferences for language and theme colors MUST remain first-class behavior.
Locale changes MUST update the `locale` cookie and authenticated user language
preferences when applicable. Color customization MUST continue to use CSS
variables so layouts, shared components, and runtime preference loading remain
consistent across public and private pages.

**Rationale**: Multilingual navigation, authentication flows, notes, financial
screens, and settings are core to the product surface; UI text must never be hardcoded.

### IV. Secure Session and Server-Side Integration

Authentication tokens MUST be stored only in HttpOnly cookies and MUST NOT be
read by client-side code, persisted in localStorage, or exposed in rendered
payloads or `NEXT_PUBLIC_` environment variables. Server-side routes MUST read
the cookie, validate the session when needed, and forward the token to external
APIs as a Bearer token.

Private pages MUST remain protected by `src/middleware.ts` and server-side user checks.
Public authentication pages MUST redirect authenticated users away from login,
registration, and password recovery. Public note reads MAY attempt the external public
endpoint first, but private note access MUST require an active session.

**Rationale**: The application strictly isolates session ownership from UI state to
preserve confidentiality and predictable access control.

### V. Consistent UI System and Runtime Performance

UI work MUST preserve the existing shared visual system based on Tailwind CSS,
CSS variables, `ui-*` component classes, the default peach/brown theme, and
runtime user preferences. New pages and components MUST reuse the established
loading, error, success, card, input, button, navigation, and floating-page
patterns unless a specification explicitly justifies a new pattern.

Client-side components MUST avoid unnecessary network waterfalls and stale state
updates. Related and independent data SHOULD be fetched in parallel. Async
effects MUST prevent updates to unmounted components. Heavy client components
(such as rich text editors or complex charts) SHOULD use dynamic loading (`next/dynamic`)
to avoid blocking initial page rendering.

**Rationale**: Coherent runtime styling and snappy page loads prevent UI fragmentation
and deliver a premium user experience.

### VI. Simplicity & Focused Change

Implement the minimum change that satisfies the specification. Do not refactor unrelated
modules, rename broad surfaces, or introduce new abstractions unless the feature explicitly requires them.

Prefer existing UI primitives (`src/components/ui/`, form controls) and shared hooks
over duplicate implementations. Avoid over-engineering and YAGNI violations.

Avoid isolated adjustments in a single layer when changes touch shared contracts,
route handlers, headers, cookies, or token flows — those changes MUST be coordinated
end-to-end across frontend and BFF boundaries.

**Rationale**: Small, focused diffs reduce QA risk, make PR reviews clear, and prevent
unintended side effects in production.

### VII. Manual Quality Assurance

Automated test suites are **not** configured in this repository. Quality assurance relies on
manual validation and production QA unless the user explicitly requests automated tests.

Business logic MUST still be written for future testability: low coupling, high cohesion,
pure functions where practical, and thin route handlers delegating to reusable modules.

`tasks.md` MUST NOT plan or request automated tests (unit, integration, e2e) unless the user
explicitly asks for them. Instead, `tasks.md` MUST include **manual validation steps**
equivalent to the acceptance scenarios in `spec.md`.

**Rationale**: Reflects project QA practices while keeping code structurally testable if
test automation is introduced later.

---

## Security Requirements

- Sensitive data (tokens, passwords, secrets, PII) MUST NOT appear in source code, logs,
  user-visible error messages, client-side storage, or `NEXT_PUBLIC_` variables.
- External API endpoints and backend URLs MUST be read server-side from environment variables
  and fail clearly when required configuration is missing.
- Mutating routes MUST require an authenticated cookie when operating on protected resources.
- Cookie options MUST keep `httpOnly`, `sameSite: "lax"`, root path scope (`path: "/"`), and
  production-only `secure` mode unless a security specification explicitly modifies them.
- Client-side components MUST NOT call external service URLs directly.
- Generic `try/catch` blocks that swallow errors without proper normalization, safe logging,
  or user-safe messaging are prohibited.

## Performance Requirements

- Route handlers and client-side fetches that depend on fresh user data MUST use `no-store`
  semantics or an equivalent cache-busting/revalidation strategy.
- Related and independent requests SHOULD run in parallel (e.g., fetching categories, budgets, and expenses concurrently).
- Client-only rendering MUST be used for components that create hydration mismatch risks,
  including locale switching, runtime theme preferences, and rich text editing.
- Shared loading states MUST avoid covering persistent navigation unless the page is intentionally standalone.
- Additional third-party dependencies MUST be strictly justified by user-facing value, bundle impact,
  and consistency with the current stack.
- The production build (`npm run build`) and linting (`npm run lint`) MUST pass cleanly without warnings or errors.

## Spec Kit Workflow: Complex vs. Simple Features

Specification-Driven Development (SDD) is the mandatory workflow for this project.
To balance thoroughness with efficiency, features are classified by complexity to scale
the number of generated design artifacts:

### 1. Feature Classification Criteria

| Classification | Criteria / Triggers | Required Artifacts |
|---|---|---|
| **Complex Feature** *(Standard Flow)* | • New domain modules or major business capabilities<br>• New database entities, schemas, or migrations<br>• Authentication, authorization, or session changes<br>• External API integrations or BFF contract shifts<br>• High-risk security or cross-cutting architectural changes | Full artifact pipeline:<br>1. `spec.md`<br>2. `research.md`<br>3. `plan.md`<br>4. `data-model.md`<br>5. `requirements.md`<br>6. `tasks.md` |
| **Simple Feature** *(Streamlined Flow)* | • Pure UI/UX styling, layout, or cosmetic improvements<br>• Static page additions (e.g., informational, terms, policy pages)<br>• Localization dictionary / copy updates<br>• Minor bug fixes or enhancements to existing forms/endpoints without new entities or architectural ambiguity | Streamlined artifact pipeline:<br>1. `spec.md`<br>2. `plan.md`<br>3. `tasks.md`<br>*(Omits `research.md`, `data-model.md`, and `requirements.md` to avoid unnecessary overhead)* |

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
  - `tasks.md` MUST NOT include `dotnet` commands for application validation, build, or execution.
  - `tasks.md` MUST NOT include automated test generation/execution unless explicitly requested by the user.
- **Manual QA Tasks**: Every user story in `tasks.md` MUST contain explicit manual verification tasks mapping to the acceptance criteria in `spec.md`.

---

## Anti-Regression Checklist

Before completing an implementation or marking a feature done, verify:

- [ ] **BFF Boundary**: Client-side code does not call external services directly; all requests flow through internal Next.js API route handlers.
- [ ] **Auth & Session**: HttpOnly cookies preserved; tokens never exposed to client storage or `NEXT_PUBLIC_`; private routes protected by middleware and server-side checks.
- [ ] **Localization (i18n)**: All user-facing strings added to the centralized localization catalog across supported locales (`en`, `pt`, `es`) with English fallback; no hardcoded UI strings.
- [ ] **Theme & UI**: Consistent use of CSS variables, Tailwind `ui-*` classes, and runtime user preference support.
- [ ] **Type Safety & Contracts**: Strict TypeScript enforced; no `var`, `any`, or suppressed types; input validation handled via Zod or typed contracts.
- [ ] **Error Handling**: No swallowed/generic `try/catch`; clear localized user feedback and normalized API errors.
- [ ] **Performance**: Parallel fetching used where applicable; `no-store` or proper caching for fresh user data; heavy components loaded dynamically if needed.
- [ ] **Spec Kit Compliance & Closure**: All tasks verified through manual validation steps; feature status in `spec.md` updated from `Draft` to `Done`.

---

## Post-Implementation Closure

After the AI agent completes all tasks in `tasks.md` for a feature, the agent MUST
automatically update the `Status` property in that feature's `spec.md` from `Draft` to `Done`.

This update is mandatory as the final systemic step of the AI implementation cycle and is
independent of subsequent manual QA performed by the developer in staging or production.

---

## Governance

This constitution is the authoritative source for Organizando Tudo Web
architectural and development principles. Specifications, plans, tasks, code
changes, PR reviews, and documentation MUST be checked against these guidelines
before an implementation is considered complete.

The principles described here represent practices already evident in the
project codebase. Any new feature, architectural change, or code review MUST
adhere to these guidelines to avoid introducing architectural regressions,
domain inconsistencies, sensitive data exposure, or security failures.

Significant changes to these practices MUST be justified in versioning,
including the reason for the change, affected principles or sections, version
impact, and required updates to Spec Kit templates or project documentation.
Versioning follows semantic rules:

- MAJOR: incompatible governance changes or removal/redefinition of core principles.
- MINOR: new principles, new mandatory sections, or material expansion of guidance.
- PATCH: clarifications, wording improvements, and non-semantic corrections.

Compliance review MUST verify preservation of the BFF boundary, server-side
session ownership, i18n consistency, theme preferences, security rules, the
scaled Spec Kit flow (simple vs. complex), manual quality, and anti-regression checks.

**Version**: 1.3.0 | **Ratified**: 2026-05-23 | **Last Amended**: 2026-08-18
