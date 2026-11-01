<!-- Rapider Official App SOW & Specification | App #104: Application Error Tracking (application-error-tracking) | Domain: DEV -->

# #104 Application Error Tracking (`application-error-tracking`) — Scope of Work (SOW)

- **App Number & Name:** `#104 Application Error Tracking`
- **Proposed App Key:** `application-error-tracking`
- **Domain / Suite & Model Prefix:** `DEV` (`Dev*`) — Category `11. Software Development & DevOps`
- **Representative Benchmark (`P1`):** `Sentry`
- **Combined Feature Catalog:** [../combined-features/104-application-error-tracking-features.md](../combined-features/104-application-error-tracking-features.md)

---

## 1. Executive Outcome & Target Audience

**Application Error Tracking (`application-error-tracking`)** gives software engineering, mobile, frontend, backend, and SRE teams a real-time, code-level crash and exception intelligence hub inside the Rapider `DEV` suite. Instead of sifting through terabytes of unstructured raw text logs after customers complain, developers capture unhandled exceptions, panics, rejected promises, and mobile ANR/OOM crashes with full symbolicated stack traces, chronological pre-crash breadcrumbs, error-triggered DOM replay references, suspect commit attribution, and quantified release stability scores.

- **SME & Startup Fast-Path:** A 5-person product team creates a `DevErrorService`, copies a single DSN snippet into their web/mobile/API app, and immediately gets out-of-the-box stack deduplication, automatic PII scrubbing, release crash-free tracking, and one-click bug creation in Rapider Planning (`ApmIssue`)—with zero custom fingerprinting or query rules required.
- **Enterprise Progressive Disclosure:** Multi-squad organizations unlock declarative server-side `DevGroupingRule` fingerprinting, `CODEOWNERS`-driven `DevOwnershipRule` routing, release stability gates (`targetStabilityThreshold` / `criticalStabilityThreshold`), cross-service OpenTelemetry `traceId` correlation, automated `#105` feature-flag kill-switch hooks, and SOC2/HIPAA/GDPR field-level redaction policies.
- **Measurable Business & Engineering Outcomes:**
  1. Reduce Mean Time to Resolution (MTTR) for production application exceptions by **50%–70%** via symbolicated stack frames, breadcrumbs, and suspect commits.
  2. Achieve **99.5%+ Crash-Free Session Rates** on web and mobile releases by gating phased rollouts on real-time `DevReleaseHealth` stability scores.
  3. Eliminate **95%+ alert fatigue** by deduplicating millions of repetitive exception occurrences into canonical `DevErrorIssue` records with conditional snooze and velocity thresholds.
- **Top 6 Global Benchmarks Synthesized (`P1`–`P6`):** `Sentry` (`P1` — Representative Product), `Datadog Error Tracking` (`P2`), `BugSnag` (`P3`), `Rollbar` (`P4`), `Microsoft Azure Application Insights` (`P5`), and `ServiceNow Cloud Observability / Lightstep` (`P6`).

---

## 2. In-Scope, Out-of-Scope & Suite Ownership Boundaries

### In-Scope
- Provisioning application/service DSNs, scoped ingestion keys, CORS origin allowlists, inbound spike quotas, and server-side PII/secret scrubbing policies (`AET-001`, `AET-004`, `AET-005`).
- Ingesting structured exception envelopes (web, backend, mobile ANR/OOM), uploading release debug symbols/source maps to Platform Files, and symbolicating stack frames (`AET-002`, `AET-003`, `AET-006`).
- Deterministic & custom stack-trace fingerprinting, issue deduplication, merge/unmerge, triage state machine (`UNRESOLVED`, `FOR_REVIEW`, `RESOLVED`, `IGNORED`), automatic regression detection, and conditional snooze rules (`AET-007`–`AET-012`).
- Diagnostic inspection of chronological breadcrumbs, sanitized runtime/request context, W3C `traceId`/`spanId` links, error-triggered DOM session replay references, and user crash feedback (`AET-013`–`AET-016`, `AET-025`).
- Release Health crash-free user/session stability scoring, suspect commit attribution, cron/job check-in monitoring, alert rules, and grounded AI root-cause/fix proposals (`AET-017`–`AET-024`, `AET-026`–`AET-028`).

### Out-of-Scope
- Full host/container CPU, memory, disk, Kubernetes, and network infrastructure metrics (owned by `#083 Cloud Infrastructure Monitoring`).
- High-volume unstructured operational syslog archiving and SIEM threat correlation (owned by log management / security suites).
- Agile sprint planning, story points, velocity charts, and general backlog management (owned by `APM` / `planning`).
- Customer support omnichannel ticketing and SLA timers (owned by `SUP` / `support`).
- Hosting or executing feature flag SDK evaluation engines (owned by `#105 Feature Flag Management`).

### Cross-Suite Ownership & Integration Contract

| Domain / App | Owns (Canonical System of Record) | Integration Contract with `#104` (`application-error-tracking`) |
| :--- | :--- | :--- |
| **`#104` (`application-error-tracking`)** | `DevErrorService`, `DevErrorIssue`, `DevErrorEvent`, `DevSymbolArtifact`, `DevReleaseHealth`, `DevGroupingRule`, `DevOwnershipRule`, `DevErrorAlertRule` | Exposes error triage states, release stability scores, and Kafka events (`dev.error.issue.regressed`, `dev.error.stability.breached`). |
| **`APM` (`planning` / `apm`)** | `ApmProject`, `ApmEpic`, `ApmSprint`, `ApmIssue` | `#104` links `DevErrorIssue.linkedApmIssueId` to canonical `ApmIssue`; closing the `ApmIssue` emits an event that transitions `DevErrorIssue` to `RESOLVED_IN_NEXT_RELEASE`. |
| **`SUP` (`support`)** | `SupCustomer`, `SupTicket`, `SupSlaPolicy` | `#104` stores optional `SupTicket` references on `DevErrorIssue` so customer-reported crashes reflect engineering fix progress without duplicating tickets. |
| **`#105` (`feature-flag-management`)** | `DevFeatureFlag`, `DevFlagEnvironmentRule` | `DevErrorEvent` records evaluated flag keys/variants in context; critical crash spikes on a flagged release emit an event / Home `WorkItem` proposing kill-switch disablement in `#105`. |
| **`#106` (`automated-software-testing`)** | `DevTestCase`, `DevTestRun` | Grounded AI reproduction steps (`AET-027`/`AET-028`) can hand off a draft regression test specification to `#106`. |
| **Platform Core Services** | Identity/RBAC, Files/Drive, Notebooks, Home `WorkItem`s, `projectChannelMessage` | Binary source maps/dSYMs and masked replay segments reside in Platform Files; critical release gate breaches create Home `WorkItem`s; AI & team triage chats run on `projectChannelMessage`. |

---

## 3. Primary Web Pages & Sidebar Navigation Architecture (12 Pages)

Every visible sidebar section strictly enforces the **Minimum 3 Items Rule** (`Overview & Triage` = 3 items, `Releases & Services` = 3 items, `Governance & Rules` = 3 items; detail and drawer routes set `displayOnMenu: false`).

| # | Route | Page Title | Sidebar Section | Menu Visible | Key Components & UX Standards |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `dashboard` | Error Pulse & Stability Overview | `Overview & Triage` | `true` (Order 1) | KPI cards (`countSWR`/`findSWR`) for Unresolved Issues, 24h Events, Crash-Free Session %, Regressions; top noisy services chart; recent critical alerts list. |
| 2 | `issues` | Issue Triage Inbox | `Overview & Triage` | `true` (Order 2) | Dual List + `<rapider-kanban>` Triage Board (`UNRESOLVED`, `FOR_REVIEW`, `RESOLVED`, `IGNORED`); `#activeFilterTagsBar` with `Clear all`; clickable primary issue title (`issue-detail`); bulk Resolve/Snooze/Merge toolbar. |
| 3 | `analytics` | Error Explorer & Job Monitors | `Overview & Triage` | `true` (Order 3) | Tag facet distribution explorer, error velocity time-series charts, cron/scheduled job check-in status table (`AET-024`), and Save-to-Notebook export. |
| 4 | `releases` | Release Health & Stability | `Releases & Services` | `true` (Order 4) | Version comparison matrix displaying `Decimal128` User & Session Stability Scores, adoption curves, new issues count, and target/critical gate badges; clicks through to `release-detail`. |
| 5 | `services` | Monitored Services & DSNs | `Releases & Services` | `true` (Order 5) | Registry of `DevErrorService` apps (Web, API, iOS, Android), DSN copy widget, inbound quota meter, Edit (`icon + text`) and Delete (`icon-only`) row actions. |
| 6 | `symbols` | Debug Symbols & Source Maps | `Releases & Services` | `true` (Order 6) | Release-scoped artifact table (`DevSymbolArtifact`) showing source map / dSYM / ProGuard bundle status, symbolication health check, and Platform Files upload trigger. |
| 7 | `alerts` | Alert Rules & Escalations | `Governance & Rules` | `true` (Order 7) | `DevErrorAlertRule` table for first-seen, regression, velocity-spike, and stability-drop triggers with webhook, Home `WorkItem`, and `<rapider-phone-input>` SMS escalation targets. |
| 8 | `grouping-ownership` | Fingerprinting & Ownership Rules | `Governance & Rules` | `true` (Order 8) | Tabbed governance editor for declarative `DevGroupingRule` fingerprint expressions and `CODEOWNERS`-style `DevOwnershipRule` auto-assignment mappings. |
| 9 | `privacy-scrubbing` | PII Scrubbing & Spike Quotas | `Governance & Rules` | `true` (Order 9) | Workspace & per-service data redaction rules (credit cards, tokens, custom regexes, IP masking), inbound browser/bot filters, and immutable policy audit trail. |
| 10 | `issue-detail` | Issue Diagnostic & Stack Trace | *(Hidden)* | `false` | Symbolicated stack frame accordion (in-app vs. framework toggle), breadcrumb timeline, tags/flags context, DOM replay viewer link, `ApmIssue`/`SupTicket` linker, and AI Seer thread (`projectChannelMessage`). |
| 11 | `release-detail` | Release Stability Breakdown | *(Hidden)* | `false` | Version deep-dive with suspect commits list, new vs. regressed issues introduced in this release, environment adoption breakdown, and rollout gate status. |
| 12 | `service-drawer` | Create / Edit Monitored Service | *(Hidden)* | `false` | Sliding form drawer for `DevErrorService` creation/editing; enforces `try/catch/finally` button state reset, SWR cache invalidation, broadcast, and auto-close on Save/Cancel. |

**Mandatory Rapider UI Compliance across all 12 Pages:**
- Initial loads exclusively call `window.rapiderApi.findSWR(entity, filter)` and `countSWR(entity, where)` with zero redundant `isDeleted` filter clauses; local state filters `item.isDeleted !== true` and executes optimistic UI updates on triage/edit/delete.
- Data tables never render a standalone "View" button in `Actions`; clicking the primary identifier (`issueTitle`, `version`, `serviceName`, `ruleName`) navigates to detail; `Edit` uses `<i class="fas fa-edit mr-1"></i><span>Edit</span>` and `Delete` uses icon-only `<i class="fas fa-trash-alt"></i>` with `aria-label="Delete"`.
- Primary and destructive confirmation buttons enforce `#ffffff !important` text contrast; native `window.alert`, `window.confirm`, and `window.prompt` are strictly prohibited.

---

## 4. Canonical Data Models (`Dev*` — 8 Models)

All models omit `projectId` (resolved from authenticated Rapider host context), use backend-generated UUIDs (`00000000-xxxx-xxxx-xxxx-xxxxxxxxxxxx` in deterministic seed fixtures), include `version: number` for optimistic concurrency, and store precision ratios as `Decimal128`.

| Model Name | Key Fields & Types | Relationships & External Refs | Invariants & Lifecycle |
| :--- | :--- | :--- | :--- |
| **`DevErrorService`** | `id` (UUID), `name` (String), `slug` (String), `platformType` (`WEB_JS` \| `NODE` \| `PYTHON` \| `JAVA` \| `GO` \| `IOS` \| `ANDROID` \| `FLUTTER`), `dsnPublicKey` (String), `allowedOrigins` (String[]), `scrubPiiEnabled` (Boolean), `scrubPatterns` (String[]), `hourlyEventQuota` (Integer), `samplingRate` (`Decimal128`), `status` (`ACTIVE` \| `PAUSED` \| `ARCHIVED`), `version` (Integer) | Refs owner `userId` / `HrDepartment` via Platform Identity | `slug` unique per workspace; `scrubPiiEnabled` defaults `true`; rotating `dsnPublicKey` retains grace window if configured; `samplingRate` in `[0.0000, 1.0000]`. |
| **`DevErrorIssue`** | `id` (UUID), `shortId` (String, e.g., `API-402`), `serviceId` (UUID), `fingerprintHash` (String), `exceptionType` (String), `title` (String), `culprit` (String), `level` (`FATAL` \| `ERROR` \| `WARNING` \| `INFO`), `isUnhandled` (Boolean), `status` (`UNRESOLVED` \| `FOR_REVIEW` \| `RESOLVED` \| `RESOLVED_IN_NEXT_RELEASE` \| `IGNORED`), `snoozeUntil` (DateTime?), `snoozeOccurrenceLimit` (Integer?), `occurrenceCount` (Long), `affectedUserCount` (Long), `firstSeenAt` (DateTime), `lastSeenAt` (DateTime), `firstSeenRelease` (String?), `lastSeenRelease` (String?), `suspectCommitSha` (String?), `version` (Integer) | Belongs to `DevErrorService`; optional `assigneeUserId` (Platform Identity), `linkedApmIssueId` (`ApmIssue`), `linkedSupTicketIds` (`SupTicket[]`), `threadChannelId` (`projectChannelMessage`) | Unique `(serviceId, fingerprintHash)` while active; ingesting an event in a newer semantic release when `status = RESOLVED` automatically transitions `status -> UNRESOLVED` and increments `regressionCount`. |
| **`DevErrorEvent`** | `id` (UUID), `eventId` (String), `issueId` (UUID), `serviceId` (UUID), `environment` (String), `releaseVersion` (String?), `occurredAt` (DateTime), `symbolicatedFrames` (JSON[]), `breadcrumbs` (JSON[]), `tags` (JSON), `runtimeContext` (JSON), `activeFlagVariants` (JSON?), `traceId` (String?), `spanId` (String?), `scrubbingApplied` (String[]), `userFeedbackNote` (String?) | Belongs to `DevErrorIssue` and `DevErrorService`; optional `replayRefId` (`DevSessionReplayRef`), `activeFlagVariants` refs `#105` keys | **Append-only immutable evidence** once persisted after PII scrubbing; raw unredacted secrets are never written to disk or event streams. |
| **`DevSymbolArtifact`** | `id` (UUID), `serviceId` (UUID), `releaseVersion` (String), `artifactType` (`SOURCE_MAP` \| `DSYM` \| `PROGUARD` \| `ELF_DWARF`), `bundleName` (String), `checksumSha256` (String), `fileId` (UUID), `frameCount` (Integer), `verificationStatus` (`VERIFIED` \| `INVALID` \| `EXPIRED`), `uploadedAt` (DateTime), `version` (Integer) | Belongs to `DevErrorService`; `fileId` refs canonical Rapider Platform Files/Drive | Immutable binary payload per `(serviceId, releaseVersion, bundleName, checksumSha256)`; deleting or expiring never mutates previously symbolicated `DevErrorEvent` records. |
| **`DevReleaseHealth`** | `id` (UUID), `serviceId` (UUID), `environment` (String), `releaseVersion` (String), `totalSessions` (Long), `crashedSessions` (Long), `erroredSessions` (Long), `totalUsers` (Long), `crashedUsers` (Long), `crashFreeSessionRate` (`Decimal128`), `crashFreeUserRate` (`Decimal128`), `targetStabilityThreshold` (`Decimal128`), `criticalStabilityThreshold` (`Decimal128`), `gateStatus` (`HEALTHY` \| `WARNING` \| `CRITICAL_BREACH`), `deployedAt` (DateTime), `version` (Integer) | Belongs to `DevErrorService`; optional `commitRangeRef` (Git repo `#099`), `rolloutFlagId` (`#105` `DevFeatureFlag`) | `crashFreeSessionRate` and `crashFreeUserRate` computed with `Decimal128` precision (`0.0000`–`100.0000`); dropping below `criticalStabilityThreshold` transitions `gateStatus -> CRITICAL_BREACH` and emits a Home `WorkItem`. |
| **`DevGroupingRule`** | `id` (UUID), `serviceId` (UUID), `name` (String), `priorityOrder` (Integer), `matchExpression` (String), `targetFingerprintTemplate` (String), `isActive` (Boolean), `matchedCount` (Long), `version` (Integer) | Belongs to `DevErrorService` | Evaluated deterministically in ascending `priorityOrder` during ingestion; applies prospectively to incoming events without rewriting historical event hashes unless explicit re-group command runs. |
| **`DevOwnershipRule`** | `id` (UUID), `serviceId` (UUID), `priorityOrder` (Integer), `matcherType` (`PATH_GLOB` \| `MODULE` \| `TAG` \| `URL_PATTERN`), `pattern` (String), `ownerType` (`USER` \| `TEAM`), `ownerRefId` (UUID), `autoAssign` (Boolean), `isActive` (Boolean), `version` (Integer) | Belongs to `DevErrorService`; `ownerRefId` refs Platform Identity User or `HrDepartment` / Team | Validates `ownerRefId` exists in workspace RBAC before activation; highest-priority matching rule assigns `DevErrorIssue.assigneeUserId` when unassigned. |
| **`DevErrorAlertRule`** | `id` (UUID), `serviceId` (UUID), `name` (String), `environment` (String), `triggerType` (`FIRST_SEEN` \| `REGRESSION` \| `VELOCITY_SPIKE` \| `CRASH_FREE_DROP` \| `CRON_MISSED`), `thresholdValue` (`Decimal128`), `windowMinutes` (Integer), `cooldownMinutes` (Integer), `createHomeWorkItem` (Boolean), `killSwitchFlagId` (UUID?), `notificationTargets` (JSON), `status` (`ENABLED` \| `MUTED`), `lastTriggeredAt` (DateTime?), `version` (Integer) | Belongs to `DevErrorService`; optional `killSwitchFlagId` refs `#105` `DevFeatureFlag` | Deduplicates repeated firing within `cooldownMinutes`; SMS targets validated via `<rapider-phone-input>` E.164 format. |

---

## 5. Core End-to-End User Journeys

1. **SME 3-Minute Onboarding & First Exception Triage:**
   - Lead Developer opens `#/services`, clicks **New Monitored Service** (opens `service-drawer`), enters `Checkout API` (`NODE`), and saves (`DevErrorService` created, drawer auto-closes, table refreshes optimistically).
   - Developer uploads `v1.4.0` source maps on `#/symbols` (`uploadSymbolArtifact`) and triggers a test exception.
   - Navigating to `#/issues`, the developer sees `API-101: TypeError: Cannot read properties of undefined (reading 'taxRate')`, clicks the primary link to open `#/issue-detail?id=...`, inspects the symbolicated stack line (`pricingEngine.ts:84`) and pre-crash HTTP breadcrumbs, and clicks **Create APM Bug**—creating a linked `ApmIssue` in one click.
2. **Release Stability Breach -> Home `WorkItem` -> Feature Flag Kill-Switch Handoff:**
   - Mobile team ships `ios-app@3.2.0` behind feature flag `new-apple-pay-sheet`. Within 15 minutes, OOM/NSException crashes drop `DevReleaseHealth.crashFreeSessionRate` to `96.40%` (below `criticalStabilityThreshold = 98.50%`).
   - `evaluateReleaseStability` transitions `gateStatus -> CRITICAL_BREACH`, fires `DevErrorAlertRule`, and publishes a shared Rapider Home `WorkItem` assigned to the Release Manager with a recommended action: *Disable flag `new-apple-pay-sheet` in `#105`*.
   - Release Manager reviews `#/release-detail`, confirms the correlation in `activeFlagVariants`, and approves the guarded kill-switch action (`proposeFlagKillSwitch`), halting crashes within seconds.
3. **Resolved Issue Regression Across Releases:**
   - Engineer marks `API-101` as `RESOLVED_IN_NEXT_RELEASE` (`v1.4.1`). Deployment `v1.4.1` runs cleanly for 3 days, then `v1.5.0` re-introduces the bug.
   - `ingestErrorEvent` detects `releaseVersion = 1.5.0 > 1.4.1` on a resolved fingerprint, transitions `API-101` back to `UNRESOLVED` (`REGRESSED`), attaches Suspect Commit `a94f21c`, re-notifies the owner via `DevOwnershipRule`, and reopens/comments on the linked `ApmIssue`.
4. **Grounded AI Root-Cause Investigation (`projectChannelMessage`):**
   - From `#/issue-detail`, an engineer clicks **Analyze with Rapider AI**. The backend gathers the already-scrubbed `DevErrorEvent` stack frames, breadcrumbs, and suspect commit diff, posting a grounded root-cause breakdown onto the issue's `projectChannelMessage` thread citing exact frame lines and proposing a null-guard unit test—never auto-committing code without human approval.
5. **List Filtering, Click-Through Navigation & Back State Restoration:**
   - On `#/issues`, a triager types `"taxRate"` into search and clicks filter pill `Environment: prod` + `Status: UNRESOLVED`. `#activeFilterTagsBar` renders `Search: "taxRate" ✕`, `Env: "prod" ✕`, `Status: "UNRESOLVED" ✕` and `[Clear all]`.
   - Clicking `API-101` navigates cleanly to `#/issue-detail?id=...` (no `<base href>` 404 diversion). Clicking `#btnGoBack` returns to `#/issues` with the exact active filters, search query, and scroll/list state preserved; clicking `Clear all` resets all pills to `ALL` and restores the full dataset.

---

## 6. Consolidated Feature Priorities & Work Packages

### `P0` Foundation (MVP Release-Ready Core)
- **WP-P0-1: Service Provisioning, Ingestion & PII Scrubbing (`AET-001`–`AET-004`):** `DevErrorService`, `DevErrorEvent`, `DevSymbolArtifact` CRUD + guarded `ingestErrorEvent` and `uploadSymbolArtifact` commands; mandatory PII/token scrubber before persistence; `#/services`, `service-drawer`, `#/symbols`, `#/privacy-scrubbing`.
- **WP-P0-2: Fingerprinting, Triage Inbox & Code Ownership (`AET-007`–`AET-012`):** Stack normalization, `DevErrorIssue` state machine, regression reopening, conditional snooze, merge/unmerge (`DevGroupingRule`), `CODEOWNERS` auto-assignment (`DevOwnershipRule`), `#/issues` (Table + `<rapider-kanban>` + `#activeFilterTagsBar`), `#/grouping-ownership`.
- **WP-P0-3: Diagnostic Stack, Breadcrumbs & APM Linking (`AET-013`, `AET-014`, `AET-019`):** `#/issue-detail` symbolicated stack viewer, breadcrumb timeline, runtime/flag context inspector, and bi-directional `ApmIssue` creation/resolution sync.
- **WP-P0-4: Release Stability Scores & Core Alerting (`AET-017`, `AET-022`):** `DevReleaseHealth` `Decimal128` crash-free calculators, `#/dashboard`, `#/releases`, `#/release-detail`, and `DevErrorAlertRule` evaluation (`#/alerts`).

### `P1` Enterprise Scale & Observability Unification
- **WP-P1-1: Mobile ANR/OOM & Inbound Spike Protection (`AET-005`, `AET-006`):** Inbound bot/browser filters, dynamic rate quotas, and mobile thermal/memory/ANR thread diagnostics.
- **WP-P1-2: Trace Correlation & Error-Triggered Replay (`AET-015`, `AET-016`):** W3C `traceId`/`spanId` cross-linking and Platform Files-backed masked DOM session replay timeline synchronization on `#/issue-detail`.
- **WP-P1-3: Suspect Commits, Support Escalation & Flag Kill-Switches (`AET-018`, `AET-020`, `AET-021`, `AET-023`):** Git commit blame correlation, `SupTicket` impact linking, Home `WorkItem` stability escalations, `#105` kill-switch webhook/event handoff, and `#/analytics` query explorer.

### `P2` Adjacent Capabilities & Grounded AI
- **WP-P2-1: Cron Check-In Monitors & Crash Feedback (`AET-024`, `AET-025`):** Scheduled worker heartbeat monitor table inside `#/analytics` and end-user crash report notes attached to `DevErrorEvent`.
- **WP-P2-2: Seer-Style Grounded AI Triage & Regression Test Handoff (`AET-026`–`AET-028`):** Root-cause synthesis, breadcrumb/replay text summarization, and human-gated patch + `#106` regression test drafts over `projectChannelMessage`.

---

## 7. Roles, Permissions & Field-Level Governance

| Role | Permitted Actions | Restricted / Field-Level Boundaries |
| :--- | :--- | :--- |
| **Developer / Triager** | View assigned & workspace `DevErrorIssue` / `DevErrorEvent` / `DevReleaseHealth`; transition triage states (`Resolve`, `Snooze`, `For Review`); link `ApmIssue` / `SupTicket`; upload `DevSymbolArtifact`; invoke AI RCA on `projectChannelMessage`. | Cannot weaken or disable PII scrubbing rules, delete `DevErrorService`, rotate production DSNs, or purge audit logs. |
| **Release / Engineering Lead** | All Developer permissions + create/edit `DevErrorService`, rotate DSN keys, author `DevGroupingRule`, `DevOwnershipRule`, and `DevErrorAlertRule`, configure `Decimal128` stability thresholds, and approve Home `WorkItem` kill-switch actions. | Cannot disable workspace-mandated global compliance redaction patterns (`scrubPiiEnabled` floor set by Security Admin). |
| **Support / QA Viewer** | Read-only access to `DevErrorIssue` summaries, triage status, affected release versions, and `SupTicket` / `ApmIssue` links; export sanitized reproduction steps to `#106`. | **Field-Level Restriction:** Raw `runtimeContext` headers, environment variables, IP metadata, and `dsnPublicKey` values are masked/hidden; zero mutation permissions on `Dev*` records. |
| **Security & Governance Admin** | Configure mandatory workspace PII/secret scrubbing patterns (`#/privacy-scrubbing`), data retention TTLs, GDPR subject-erasure runs, and inbound quota caps; view full policy audit history. | Cannot fabricate or mutate immutable `DevErrorEvent` stack evidence (may only trigger audited GDPR PII tombstoning). |
| **Scoped Ingestion / CI Identity** | Write-only `ingestErrorEvent`, `uploadSymbolArtifact`, and `recordReleaseDeployment` scoped strictly to the single `DevErrorService` matching its token. | Zero read access to stored issues, events, or workspace configuration. |

---

## 8. Technical APIs, Guarded Commands, Events & `requiredWorkflows`

### Guarded Domain Commands (Beyond Standard SWR Reads)
All state-changing commands enforce `idempotencyKey` (UUID), `correlationId` (UUID), authenticated workspace RBAC, and `expectedVersion` optimistic concurrency:
- `ingestErrorEvent(dsnKey, envelope, idempotencyKey)` -> Runs inbound filter -> PII scrubber -> symbolicator -> fingerprint matcher -> atomically upserts `DevErrorIssue` counters & regressions -> appends immutable `DevErrorEvent`.
- `triageIssue(issueId, targetStatus, snoozeSpec?, resolutionRelease?, expectedVersion, idempotencyKey)` -> Validates state transition, updates `DevErrorIssue`, syncs linked `ApmIssue` if configured.
- `mergeIssues(primaryIssueId, secondaryIssueIds[], customFingerprintRule?, expectedVersion, idempotencyKey)` -> Re-parents occurrence metrics and creates optional `DevGroupingRule`.
- `uploadSymbolArtifact(serviceId, releaseVersion, artifactType, fileId, checksumSha256, idempotencyKey)` -> Validates symbol bundle structure in Platform Files and marks `VERIFIED`.
- `evaluateReleaseStability(serviceId, environment, releaseVersion, sessionBatchMetrics, idempotencyKey)` -> Updates `Decimal128` crash-free rates, evaluates `criticalStabilityThreshold`, and emits Home `WorkItem` on breach.
- `requestAiRootCause(issueId, eventId, channelId, idempotencyKey)` -> Dispatches grounded AI worker on scrubbed event snapshot.

### Kafka Domain Events (Published & Consumed)
- **Published:** `dev.error.issue.first_seen.v1`, `dev.error.issue.regressed.v1`, `dev.error.alert.triggered.v1`, `dev.error.release_stability.breached.v1`, `dev.error.killswitch.recommended.v1`. *(Strict rule: raw stack locals and unredacted user fields are excluded from Kafka payloads—only sanitized summaries and entity UUIDs are emitted).*
- **Consumed:** `apm.issue.status_changed.v1` (auto-resolves linked `DevErrorIssue` when bug closes), `dev.deployment.completed.v1` (initializes `DevReleaseHealth` baseline), `dev.flag.toggled.v1` (annotates release timeline).

### `requiredWorkflows` in `app.manifest.json`
1. `dev-error-symbolication-and-grouping-wf` (Async OpenWhisk/Kafka symbolication of raw minified frames against `DevSymbolArtifact` + suspect commit lookup).
2. `dev-error-alert-and-stability-gate-wf` (Evaluates velocity windows and `Decimal128` stability breaches, deduplicates cooldowns, creates Home `WorkItem`s, and dispatches webhooks).
3. `dev-error-ai-seer-triage-wf` (Grounded AI root-cause & reproduction summarizer posting proposals to `projectChannelMessage`—strictly read-only against source repos, requiring human confirmation before creating any PR or `ApmIssue`).
4. `dev-error-retention-purge-wf` (Scheduled TTL cleanup of expired `DevErrorEvent` occurrences and temporary replay blobs while preserving aggregated `DevErrorIssue` counters).

---

## 9. Security, Privacy, Compliance & Non-Functional Requirements

- **Zero-Secret & PII Scrubbing by Default (GDPR / CCPA / HIPAA / SOC2):** Server-side scrubbing executes *prior* to database persistence and AI prompt construction, stripping `Authorization` headers, cookies, API keys, JWTs, SSNs, payment card PANs (Luhn check), and client IPs by default. Payloads containing translated or user-authored strings are treated strictly as untrusted data (preventing prompt injection in AI triage and XSS in UI rendering via `escapeHtml()`).
- **Right to Erasure & Configurable Retention:** Supports targeted purge of `DevErrorEvent` records matching an external user identifier hash within statutory GDPR windows, alongside configurable retention tiers (e.g., 30 / 60 / 90 days for raw events; 365 days for aggregated `DevReleaseHealth`). Cost/quota estimator displays usage transparently (e.g., \$0.00 overage surprises via deterministic inbound spike caps).
- **Immutable Audit Trail:** All changes to `DevErrorService` scrubbing rules, DSN key rotations, `DevErrorAlertRule` thresholds, and manual issue merges write append-only platform audit records with actor identity, timestamp, and before/after diffs.
- **Accessibility & Responsive Performance:** WCAG 2.2 AA compliant across `390px` (mobile on-call triage), `768px` (tablet), and `1440px` (desktop wide stack-trace inspector); keyboard-navigable stack frame accordions; `findSWR` cached inbox rendering `< 150ms` p95 and paginated API reads `< 400ms` p95.

---

## 10. Deterministic Acceptance Criteria

1. **Zero `projectId` & Platform UUID Compliance:** Inspecting all 8 `Dev*` schemas confirms zero `projectId`/`tenantId` fields, backend-generated UUIDs on create, and `00000000-xxxx-xxxx-xxxx-xxxxxxxxxxxx` UUIDs in seed fixtures.
2. **Pre-Persistence PII Scrubbing:** Submitting an error payload containing a `Bearer eyJ...` header, `password` field, and valid Luhn credit card number persists `[Filtered]` placeholders in `DevErrorEvent` and records applied scrubber names in `scrubbingApplied`.
3. **Deterministic Deduplication & Regression Reopening:** Ingesting 50 occurrences of the same stack trace increments `DevErrorIssue.occurrenceCount` to `50` on a single issue; marking it `RESOLVED` in `v1.0.0` and ingesting a matching stack trace tagged `v1.1.0` automatically transitions `status` back to `UNRESOLVED` (`REGRESSED`).
4. **Conditional Snooze Expiry:** Snoozing an issue with `snoozeOccurrenceLimit = 10` hides it from the default `UNRESOLVED` filter until the 10th new event arrives, at which point it deterministically returns to `UNRESOLVED`.
5. **Exact `Decimal128` Stability Gate & Home `WorkItem`:** Recording `9,849` crash-free sessions out of `10,000` (`98.4900%`) against `criticalStabilityThreshold = 98.5000%` transitions `DevReleaseHealth.gateStatus` to `CRITICAL_BREACH` without float rounding error and creates a linked Rapider Home `WorkItem`.
6. **Bi-Directional `ApmIssue` Contract:** Clicking **Create APM Bug** on `#/issue-detail` invokes the guarded command, populates `linkedApmIssueId`, and consuming `apm.issue.status_changed.v1` (`DONE`) transitions the `DevErrorIssue` to `RESOLVED_IN_NEXT_RELEASE`.
7. **Sidebar Minimum 3 Items Rule:** Parsing `app.manifest.json` `webPages` confirms all 3 visible sidebar groups (`Overview & Triage`, `Releases & Services`, `Governance & Rules`) each contain exactly 3 visible items (`displayOnMenu: true`), while `issue-detail`, `release-detail`, and `service-drawer` set `displayOnMenu: false`.
8. **SWR Clean Query & Optimistic Triage/Delete:** Every page mount uses `findSWR`/`countSWR` with zero `isDeleted` clauses in `where`; resolving or deleting a record updates local in-memory arrays and `<rapider-kanban>` synchronously, invalidates `sessionStorage` SWR keys, and rolls back cleanly on simulated API failure.
9. **Table Action & Contrast Compliance:** No data table contains a "View" button in `Actions`; primary identifiers navigate to `#/issue-detail` or `#/release-detail`; `Edit` renders icon + text; `Delete` renders icon-only with tooltip/`aria-label`; all primary/danger buttons compute `#ffffff` text color; zero `window.alert`/`confirm`/`prompt` calls exist in source.
10. **Active Filter Tags & Clear All:** Entering a search string or selecting environment/status pills on `#/issues` renders `#activeFilterTagsBar` with HTML-escaped dismissible chips and `Clear all`, synchronously updating both Table and `<rapider-kanban>` views.
11. **Drawer Lifecycle & `finally` Button Reset:** Saving or cancelling `service-drawer` closes the drawer automatically, broadcasts `RECORD_CREATED`/`RECORD_UPDATED`, and restores the Save button inside a `finally` block so it never freezes on `"Saving..."`.
12. **Real Click-Through & Back Restoration E2E:** Clicking an issue link on `#/issues` transitions to `#/issue-detail?id=...` without `<base href>` 404 diversion, renders the stack trace, and clicking `#btnGoBack` restores `#/issues` with active filters intact.
13. **Grounded AI Safety Boundary:** Requesting AI root-cause analysis posts a citation-backed proposal on `projectChannelMessage` using only scrubbed `DevErrorEvent` data and never executes unconfirmed code changes or external provider calls when disabled by policy.
14. **Responsive & Theme Parity:** Deterministic light/dark `validate_theme` checks and layout assertions pass with zero horizontal overflow or overlapping controls at `390px`, `768px`, and `1440px`.
