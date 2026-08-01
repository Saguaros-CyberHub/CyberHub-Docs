# 4.11.10 API Reference

Every CiaB endpoint, grouped by router. Paths are absolute – `routes/api.js` is
mounted at `/`, and each sub-router registers its own `/api/...` prefix.

Auth column: `user` = any authenticated user · `owner` = authenticated,
plus an ownership check that instructors and admins bypass · `instructor` =
instructor or admin · `admin` = admin only · `public` = no JWT.

Unless noted otherwise, every authenticated route also passes through the
[schedule middleware](02-architecture.md#schedule-middleware), which can return
403 for students outside their group's window.

## Profiles — `/api/profiles`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/profiles` | user | List profiles (filter / paginate) |
| GET | `/api/profiles/recent` | user | Recently created |
| GET | `/api/profiles/stats` | user | Counts by type / status |
| GET | `/api/profiles/stats/summary` | user | Dashboard summary |
| GET | `/api/profiles/test` | user | Connectivity check |
| POST | `/api/profiles/generate` | user | Generate a profile inline ([02](03-profile-generation.md)) |
| GET | `/api/profiles/run-status/:progressId` | user | Poll generation progress (in-memory; 404 = expired or never started) |
| POST | `/api/profiles/upload` | admin | Register an existing profile JSON (raw body, 20 MB) |
| POST | `/api/profiles/generate-and-deploy` | admin | Generate then deploy lanes |
| GET | `/api/profiles/:id` | user | One profile |
| PUT | `/api/profiles/:id/name` | user | Rename |
| DELETE | `/api/profiles/:id` | user | Delete (cascades) |
| GET | `/api/profiles/:id/policies` | user | List generated policies |
| GET | `/api/profiles/:id/policies/print` | user | All policies, print layout |
| GET | `/api/profiles/:id/policies/:slug` | user | One policy |
| POST | `/api/profiles/:id/policies/generate` | user | Generate policies (LLM fan-out) |
| GET | `/api/profiles/:id/documents` | user | List scan documents |
| GET | `/api/profiles/:id/documents/print` | user | All documents, print layout |
| GET | `/api/profiles/:id/documents/:docType` | user | One document (`nmap` \| `nessus` \| `zap`) |

## Intakes (canonical) — `/api/intakes`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/intakes` | owner | List |
| POST | `/api/intakes/upload` | user | Upload a standalone payload (4 MB) |
| GET | `/api/intakes/by-id/:intakeId` | owner | Fetch standalone |
| PUT | `/api/intakes/by-id/:intakeId/attach` | owner | Attach to a profile |
| DELETE | `/api/intakes/by-id/:intakeId` | owner | Delete |
| GET | `/api/intakes/:profileId` | owner | Fetch by profile; lazily creates an empty v1.2 intake |
| PUT | `/api/intakes/:profileId` | owner | Autosave; recomputes completion |
| POST | `/api/intakes/:profileId/complete` | owner | Mark complete |
| GET | `/api/intakes/:profileId/export` | owner | PDF export |

## Intake form (legacy V7.2) — `/api/intake-form`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/intake-form/:profileId` | user | Fetch the 15-column response |
| PUT | `/api/intake-form/:profileId` | user | Save |
| GET | `/api/intake-form/:profileId/status` | user | Status + completion |
| POST | `/api/intake-form/:profileId/complete` | user | Mark complete |
| GET | `/api/intake-form/:profileId/export` | user | PDF export |

## Real-client intake (legacy blob) — `/api/real-client/intake`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/api/real-client/intake/upload` | user | Upload an anonymized export (4 MB, schema `1.0`) |
| GET | `/api/real-client/intake` | owner | List (students see only their own) |
| GET | `/api/real-client/intake/:id` | owner | Detail |
| PUT | `/api/real-client/intake/:id/link` | instructor | Link to a `crucible_challenge` |
| DELETE | `/api/real-client/intake/:id` | owner | Delete |
| POST | `/api/real-client/intake/:id/generate-profile` | instructor | Build a profile from intake + challenge + filler counts (no AI) |
| POST | `/api/real-client/intake/:id/synthesize-challenge` | instructor | Dry-run challenge spec; writes nothing |

## Clinic Risk Assessment — `/api/clinic-risk-assessment`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/clinic-risk-assessment/frameworks` | user | CIS IG1 + NIST CSF 2.0 catalogs |
| GET | `/api/clinic-risk-assessment/pickable` | owner | Landing-page picker (profiles + real-client intakes) |
| POST | `/api/clinic-risk-assessment/from-intake/:intakeId` | owner | Promote a real-client intake to a profile (idempotent) |
| GET | `/api/clinic-risk-assessment/:profileId` | owner | Dashboard bundle |
| GET | `/api/clinic-risk-assessment/:profileId/report-data` | owner | Full bundle for the standalone HTML report |
| POST | `/api/clinic-risk-assessment/:profileId/export` | owner | PDF export; accepts client-rendered chart PNGs (10 MB) |
| PUT | `/api/clinic-risk-assessment/:profileId/report` | owner | Update exec summary, branding, CSF scores |
| POST | `/api/clinic-risk-assessment/:profileId/findings` | owner | Create a finding |
| PUT | `/api/clinic-risk-assessment/:profileId/findings/:findingId` | owner | Update |
| DELETE | `/api/clinic-risk-assessment/:profileId/findings/:findingId` | owner | Delete |
| GET/POST | `/api/clinic-risk-assessment/:profileId/assets` | owner | Asset register list / create |
| PUT/DELETE | `/api/clinic-risk-assessment/:profileId/assets/:assetId` | owner | Update / delete |
| POST | `/api/clinic-risk-assessment/:profileId/scenarios/:key/instantiate` | owner | Library scenario → finding |
| GET | `/api/clinic-risk-assessment/:profileId/poam.csv` | owner | POA&M CSV export |
| GET/PUT | `/api/clinic-risk-assessment/:profileId/insurance-readiness` | owner | Readiness scorecard |
| GET/POST | `/api/clinic-risk-assessment/:profileId/snapshots` | owner | List / create snapshots |
| POST | `/api/clinic-risk-assessment/:profileId/findings/:findingId/fair` | owner | FAIR-lite Monte Carlo; persists to the finding |
| PUT | `/api/clinic-risk-assessment/:profileId/findings/:findingId/owasp` | owner | OWASP factor rollup; persists |
| GET | `/api/clinic-risk-assessment/scenarios` | user | Threat-scenario library; seeds the catalog on first call |
| POST | `/api/clinic-risk-assessment/utils/ale-sle-aro` | user | ALE/SLE/ARO calculator; stateless |

!!! warning "Route order in this router"
    `/frameworks`, `/scenarios` and `/pickable` are declared in a STATIC ROUTES
    block at the top of
    [routes/clinic-risk-assessment.js](https://github.com/Saguaros-CyberHub/CyberCore/blob/main/front-end/modules/crucible/plugins/ciab/routes/clinic-risk-assessment.js),
    ahead of `/:profileId`. Express matches in registration order, so a literal
    single-segment path added below `/:profileId` is swallowed: the request
    lands in the profile handler with `profileId` set to the literal, which
    then fails uuid parsing and returns 500. `/scenarios` had exactly that bug
    until it was moved up. Add new literal paths to the static block, not to
    the bottom of the file.

    Two-segment literals such as `/utils/ale-sle-aro` are unambiguous, since
    every `/:profileId/…` route has a literal second segment.

## CIS RAM — `/api/cis-ram`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/cis-ram/:profileId` | owner | Workbook bundle; lazily creates the envelope + 56 rows |
| PUT | `/api/cis-ram/:profileId` | owner | Update `acceptable_risk_score` (1–9), `impact_criteria`, `status` |
| PUT | `/api/cis-ram/:profileId/safeguards/:safeguardNum` | owner | Update one safeguard row (fixed column allow-list) |

## Progress — `/api/progress`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/progress/:profileId` | user | All 8 parts + summary; enriches reviewer email cross-DB |
| PUT | `/api/progress/:profileId/:partNumber` | user | Upsert part content (1–8) |
| POST | `/api/progress/:profileId/:partNumber/submit` | user | Submit for review |

## Interview — `/api/interview`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/api/interview/start` | user | Start a session; returns the stakeholder roster |
| POST | `/api/interview/:sessionId/message` | user | Ask a question |
| POST | `/api/interview/:sessionId/end` | user | End the session |
| GET | `/api/interview/sessions/:profileId` | user | The caller's sessions |
| GET | `/api/interview/stakeholders/:profileId` | user | Roster only |

## Instructor — `/api/instructor`

Every route is `instructor`. This mount does not apply the schedule
middleware.

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/instructor/dashboard` | Roster with profiles, assignments, watch state, pending submissions |
| GET | `/api/instructor/test` | Connectivity check |
| GET | `/api/instructor/student/:studentId/progress` | One student's parts |
| POST | `/api/instructor/review/:progressId` | Score + feedback |
| POST | `/api/instructor/assign` | Assign a profile |
| GET | `/api/instructor/rubric/:profileId` | Grading rubric |
| GET | `/api/instructor/students/search` | Search students |
| POST | `/api/instructor/students/watch` | Add to working set |
| POST | `/api/instructor/claim-student` | Claim |
| DELETE | `/api/instructor/release-student/:studentId` | Release |
| POST | `/api/instructor/generate-examples` | Answer keys for Parts 1–8 (background) |
| GET | `/api/instructor/generation-status/:profileId` | Poll answer-key generation |
| GET | `/api/instructor/examples/:profileId` | Fetch generated examples |
| POST | `/api/instructor/generate-documents` | nmap / nessus / zap (deterministic) |
| GET | `/api/instructor/documents/:profileId` | List documents |
| GET | `/api/instructor/download/:profileId/:docType` | Download one |
| GET | `/api/instructor/packet/:profileId/pdf` | Instructor packet PDF |
| GET | `/api/instructor/vuln-cheat-sheet/:profileId` | Vuln-app attack chain + annotations (404 until a deploy runs) |
| GET | `/api/instructor/lanes` | Visible lanes |
| GET | `/api/instructor/lanes/:id/ips` | Per-VM IPs |
| GET | `/api/instructor/lanes/:id/connect` | Console details |
| PATCH | `/api/instructor/lanes/:id/internet` | Toggle lane internet |
| GET | `/api/instructor/my-groups` · `/all-groups` | Group membership |
| POST | `/api/instructor/join-group` | Join a group |
| GET/PUT | `/api/instructor/groups/:id/schedule` | Read / set the access window |
| PATCH | `/api/instructor/groups/:id/schedule/override` | Force open or closed |

## Profile deploy — `/api/profile-deploy`

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/profile-deploy/image/:token` | public | Prebuilt vuln-app image tarball. Token-gated + lab source-IP restricted |
| POST | `/api/profile-deploy/preview` | admin | Resource + cost pre-flight |
| POST | `/api/profile-deploy/deploy` | admin | Start a batch – 202, runs in background |
| GET | `/api/profile-deploy/profiles/:profileId/reservation` | admin | VXLAN reservation / slot usage |
| GET | `/api/profile-deploy/groups` | admin | List groups |
| GET | `/api/profile-deploy/groups/:groupId` | admin | Group detail |
| GET | `/api/profile-deploy/groups/:groupId/progress` | admin | Live phase + per-lane status |
| POST | `/api/profile-deploy/groups/:groupId/add-lanes` | admin | Grow within the reservation |
| POST | `/api/profile-deploy/groups/:groupId/retry/:laneId` | admin | Force-destroy + redeploy one lane |
| DELETE | `/api/profile-deploy/groups/:groupId` | admin | Tear down the group |

## Catch-all — `/api`

Served by `clinic-api.js`, which is mounted after the specific prefixes
above and applies `authenticateToken` to everything it claims.

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/api/generate` | user | Backward-compatible alias for profile generation |
| POST | `/api/chat` | optional | Clinic AI assistant (last 10 turns of client-supplied history, 768 tokens) |
| GET | `/api/health` | – | DB connectivity; 503 when unhealthy |
| GET | `/api/config` | – | Client types, difficulties, maturity levels, delivery modes |

`/api/chat` uses `optionalAuth` – it answers without a JWT. Conversation state
is not persisted server-side; the client sends whatever history it wants
considered.

## Pages — `/ciab`

Served by `pages.js`, no auth at the router level (the shell handles gating).
All responses are `no-store`. See the [page map](01-ciab-overview.md#page-map).
