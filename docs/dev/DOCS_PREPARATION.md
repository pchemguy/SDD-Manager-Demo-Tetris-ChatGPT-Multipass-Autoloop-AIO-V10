# Main documentation preparation record

## Current control

- Mode: autodoc/autoloop, explicitly requested for six predefined cumulative scope passes on 2026-10-24.
- Delivery mode: strict multipass by default. Later phases/tasks must preserve earlier IDs, names, scope, membership, order, dependencies and exits. No flexible exception granted.
- Current pass: 3; DOCS-007 accepted locally, publication/issue closure require confirmation below.
- Approval basis: user response Approve on 2026-10-24 accepts proposal 544b69a9c6bfd16fabd6c7b008fe364d837903ae and all recorded interaction proposals. Six predefined scopes/resets authorized. Later PROJECT approvals waived; remaining document checkpoints preapproved under autodoc/autoloop. Implementation/package QC not selected.
- Tracking: documentation and implementation enabled by existing root AGENTS.md. All nine standard issues and native milestone verified before any preparation write. Publication: push/read back every owned commit.
- Stop: all six passes through final DOCS-008. DOCS-009/package QC, documentation integration and implementation are outside the current automatic range.
- Reporting: Explicit startup, then Standard; Minimal/Standard/Explicit choices announced.

## Scope sequence

1. **Playable classic baseline:** falling tetrominoes, movement, rotation, gravity, line clearing, scoring, game over and restart; minimal usable game, without adopting later enhancements.
2. **Gameplay enhancements:** Hold, ghost, 7-bag, wall kicks, hard drop; default lock after one complete gravity interval from becoming grounded, including after hard drop.
3. **Conditional ghost visibility:** hide projected landing above the board midline; show in lower half.
4. **Penetrator mode:** bonus hotkey converts current falling piece until lock; crosses occupied cells without destroying them. Final placement entirely inside board and disjoint from settled cells. Hard drop and ghost use deepest valid landing through blockers. Four-line clear awards one activation, capped at three. Active piece entirely white; regular pieces nonwhite. Side-panel symbol per bonus updates on earn/spend.
5. **Session-only configuration:** side-panel controls for ghost hide/dynamic/show, hard drop on/off, next preview on/off (introduce preview here), hard-drop follow-general/immediate, general failed-down/full-interval locking, Hold on/off. Defaults proposed below. Reload resets settings in this scope.
6. **Configuration persistence:** all six settings independently resolve valid explicit URL value, then valid corresponding cookie, then default. Changes write cookie and update URL. Persist valid explicit URL values even when equal to defaults; omitted parameters retain cookies. Replace session-only reload behavior only in this scope.

## Per-pass decisions

The user approved the published brief and recorded interaction proposals on 2026-10-24 (response Approve). These originated as agent proposals; their acceptance is now human-confirmed. Future choices remain here until their applicable scope is adopted. Later material discoveries fall under autoloop delegation, with visible rationale and durable decisions.

| Origin / decision | Resolution and rationale | Source / basis | Applicability / status |
| --- | --- | --- | --- |
| Pass 1 / platform | Desktop keyboard browser game; TypeScript with Canvas board and semantic HTML controls; no backend. Canvas suits board drawing; HTML keeps controls accessible. Plain JavaScript reduces tooling but loses static contracts; a full UI framework adds little to this UI. | Agent recommendation from browser scope and minimal baseline; accepted by user 2026-10-24 | All passes; accepted |
| Pass 1 / baseline | 10-column by 20-row visible board, seven tetromino types, independent uniform piece selection, simple rotation rejecting collisions, lock on failed downward attempt, conventional line scoring and increasing gravity speed, fresh game on restart. | Agent proposal for coherent minimal classic game | Pass 1; accepted. Randomizer and locking superseded by requested Pass 2 additions when adopted |
| Pass 1 / ghost midline | With rows 0–19 and boundary at 10, show a dynamic ghost only if its uppermost occupied cell is row 10 or below. A straddling ghost stays hidden until every cell is in lower half. | Agent proposal; resolves user-requested interaction with consistent whole-piece visibility | Passes 3–6; accepted |
| Pass 1 / interval lock | Grounded means resting at a valid legal landing. Start a full interval on first ground contact. Movement/rotation that remains grounded does not restart it; leaving ground cancels it, and later contact starts a new interval. Snapshot interval at contact; freeze elapsed simulation time while page is inactive. Hard drop starts/retains the same timer and cannot refresh it repeatedly. | Agent proposal to avoid accidental instant locks and timer stalling | Passes 2–6; accepted |
| Pass 1 / penetration and Hold | Activation consumes one bonus once, only for a live regular piece in legal placement; repeat activation has no effect. Hold is disabled throughout active penetration. Ability expires only when locked; restart clears bonuses. | Agent proposal, consistent with established user preference; present for current-project approval | Passes 4–6; accepted |
| Pass 1 / penetration geometry | Occupancy may be crossed during movement and rotation; bounds still apply. Lock requires a fully legal position. Use one shared deepest-legal-placement search below current anchor for natural descent, hard drop and ghost; reject lateral/rotation candidates with no legal destination at or below them. Stop at that deepest destination rather than entering an overlapping floor position. Intermediate overlap never triggers locking; settled cells stay unchanged until legal lock/clear. | Agent proposal to make pass-through movement compatible with guaranteed valid final placement | Passes 4–6; accepted |
| Pass 1 / live settings | Ghost, preview, hard-drop enabled and Hold enabled changes take effect immediately. Preserve stored Hold content when disabled; show disabled status, restore availability when enabled. General/hard-drop lock-policy changes apply to the next spawned piece (including Hold replacement), preserving current-piece deadlines. Settings survive game restart. | Agent proposal to prevent retroactive or surprise current-piece locking | Passes 5–6; accepted |
| Pass 1 / configuration defaults | Ghost dynamic; hard drop on; preview on; hard-drop follows general; general full-interval; Hold on. Before configuration exists, use each currently supported behavior only. | Agent proposal aligned with requested enhancements | Passes 5–6; accepted |
| Pass 1 / invalid configuration | Strict allowlists. Empty, malformed or unrecognized URL/cookie values are invalid and fall through independently to next valid source. Duplicate recognized URL keys use first occurrence. Invalid URL values never overwrite cookies. Unknown query parameters and fragment retained. URL changes use replaceState; cookie failures retain session/URL usability without crashing. | Agent proposal; precedence remains exactly as requested for valid values | Pass 6; accepted |
| Pass 1 / cookie scope | One namespaced cookie per setting, Path=/, SameSite=Lax, one-year lifetime; Secure on HTTPS. Persist valid explicit URL values, including defaults. Missing URL key does not write a default over a cookie. | Agent proposal supporting reload/reopen behavior | Pass 6; accepted |
| Pass 1 / verification | Chromium headless shell primary and Playwright-managed Firefox secondary; isolated provisioning and state under BROWSER_DEV_POLICY.md. Browser installation is unnecessary during document preparation. | Existing repository browser policy; no capability claim | All implementation phases; retained policy |
| Pass 1 / versions | First delivered gameplay release 0.1.0. Increment minor version for each completed gameplay/configuration phase and patch for compatible fixes. Publish release/tag/notes only after authorized implementation phase verification and integration. No product release for proposals or document passes. | User version/release instruction plus agent version proposal | Future implementation; accepted |
| Pass 2 / inherited decisions | Carry forward accepted cross-scope decisions from Pass 1; current delta: Gameplay enhancements. Recheck applicability before authoring. | User-approved predefined sequence and autoloop delegation | Current; no protected delivery-range change |
| Pass 3 / inherited decisions | Carry forward accepted cross-scope decisions from Pass 1; current delta: Conditional ghost visibility. Recheck applicability before authoring. | User-approved predefined sequence and autoloop delegation | Current; no protected delivery-range change |

## Cross-scope decisions

| Concern | Applicable decision | Reconsideration condition |
| --- | --- | --- |
| Whole-piece ghost visibility | Ghost-midline decision, including penetrator projection; passes 3–6 | Board geometry or explicit visibility change |
| Landing and lock timing | Interval-lock and penetration-geometry decisions; passes 2–6 | New movement or time controls affect landing validity |
| Hold and ability lifetime | Penetration/Hold and live-settings decisions; passes 4–6 | Human changes ability transfer semantics |
| Runtime settings and persistence | Live-settings/defaults/invalid-configuration/cookie decisions; passes 5–6 | New setting or cross-setting constraint |
| Delivery preservation | Strict mode above; keep each earlier PLAN/TASKS range immutable as later scopes are adopted | Explicit scoped human exception only |

## Decisions and document evidence

| Task | Current evidence / decision basis |
| --- | --- |
| DOCS-001 | All four original root files retained byte for byte against initial commit 0e4dfbff6d3c6540e2300457878f12d32c131b4e. No copies needed. Task template copied and only DOCS-001 checked. Separate preparation record/HANDOFF created. Provenance evidence below. Published checkpoint 17f644b2f4983362f28779e4ca8d9cdf28ecc961 matched remote readback; issue #1 closed with evidence and verified completed. |
| DOCS-002 | Pass 1 PROJECT proposed at docs/dev/PROJECT.md, SHA-256 9ab792074afcd37db09a523909ecbf0d02a70688818c505fd05fab915c232afb; accepted by user 2026-10-24. Scope and document-boundary review passed: classic baseline only, no future enhancements imported, principal intent/scope/non-goals and proposed technology/verification choices visible. Local README links and original root-file preservation checked; no game code/tests executed. First approval also confirms proposed per-pass/cross-scope decisions for the six supplied scopes. DOCS-002 completion is recorded with this approval; issue closure follows publication. |
| DOCS-003 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-004 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-005 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-006 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-007 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-008 | Pass 1 accepted by scoped autodoc preapproval; exact state and QC in Accepted document states. |
| DOCS-009 | Not selected by autodoc/autoloop range. |

## Protected delivery ranges

| Completed pass | Checkpoint / protected scope |
| --- | --- |
| 1 | f4b41b3a7dfe236a8b40fe723e83afde37707f35; PLAN SHA-256 eca2d038739a0fb828200e00dd77effed2f5f65451cf3fe5fcfa128df05cf161; TASKS SHA-256 da26fd713bda611b9ab1f4dbade3a42607ea7531e46d3496cbac1c486133c7e9; phases 1–1 and their existing task ranges immutable. |
| 2 | ae73199eab39a89451ea748658a4c17a8325ee41; PLAN SHA-256 0c6036b2c4de6d8ce51981194961187bf4b64ae45d4eb0d3a99077cbca75b2f5; TASKS SHA-256 54f7c6615d9dfa48a89a9fbea4e10a37e5c55e5a183cdab8329328bc541e1fee; phases 1–2 and their existing task ranges immutable. |

## Accepted document states

| Pass / task | Accepted identity and evidence |
| --- | --- |
| Pass 1 / DOCS-002 | Human first approval: response Approve on 2026-10-24; exact PROJECT SHA-256 9ab792074afcd37db09a523909ecbf0d02a70688818c505fd05fab915c232afb retained unchanged from 544b69a. Brief and six-scope interaction decisions accepted; subsequent document checkpoints preapproved. Scope/intent/decision review passed. |
| Pass 1 / DOCS-003 | ARCHITECTURE SHA-256 0d457971609f3a63ea37a8ac96d984add97431d44deb7e01976c4626cd8d731f; accepted brief mapped to core/coordinator/presentation/adapters. Reviewed unique state ownership, inward dependencies, atomic transitions and deterministic seams; no future features imported. Autodoc preapproval applies. |
| Pass 1 / DOCS-004 | DECOMPOSITION SHA-256 c6fc0d1107e95ea783f76130a0685238a8a683fd5df6352ec53917de4d1d3d24; geometry/source/session/controller/renderer/status responsibilities reviewed against current architecture. State, error and lifecycle boundaries support independent verification. No unresolved structural decision; autodoc preapproval. |
| Pass 1 / DOCS-005 | SPEC SHA-256 2c533b9f43681af6cdb57002cf2905d1763897b8d91bf17882194a5dc53a19cc; B01–B05 accepted by scoped preapproval. SPEC-REVIEW-REPORT.md records current design hashes, scope/geometry/time/lifecycle/error review and Ready result. Detailed key/scoring timing choices are delegated agent precision within approved baseline. |
| Pass 1 / DOCS-006 | PLAN SHA-256 eca2d038739a0fb828200e00dd77effed2f5f65451cf3fe5fcfa128df05cf161; Phase 1 / milestones 1.1–1.2 bounded to baseline, version 0.1.0. PLAN-REVIEW-REPORT.md covers B01–B05, explicit one-delivery-milestone rationale and special review; strategy Ready, layout next. |
| Pass 1 / DOCS-007 | layout SHA-256 a01f6721a2c13fdffced043ef5fb967498fc5ea53a602933f1aca19f2209a6ac; combined PLAN/layout review appended to PLAN-REVIEW-REPORT.md with current input identities. Component/source/test/report ownership complete; future report/generated paths distinguished from existing files. Preapproved; TASKS ready to derive. |
| Pass 1 / DOCS-008 | TASKS SHA-256 da26fd713bda611b9ab1f4dbade3a42607ea7531e46d3496cbac1c486133c7e9; T-001–T-006, one delivery milestone with five implementation tasks plus special phase review. TASKS-REVIEW-REPORT.md records coverage, counts, hierarchy/dependency checks and Ready result. Autodoc preapproval; pass 1 DOCS-008 boundary, next authorized reset to pass 2. |
| Pass 2 / DOCS-002 | PROJECT.md SHA-256 cc982bc3bb91803121b2a14befbec1ce2f7091e51e70d34a1946c94190464609; Complete current brief includes Hold, ghost, 7-bag, wall kicks, hard drop and interval lock; no conditional visibility/penetrator/configuration adopted yet. Inherited interaction decisions reviewed; no protected delivery changes. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-003 | ARCHITECTURE.md SHA-256 9c4237b24e91af4b32914048962c2d1b3efe4e5bd18af68bd6814dcaac1df0b6; Reviewed core ownership of bag/Hold/rotation/landing/lock timing and common ghost/hard-drop projection; browser remains a consumer. Existing inward dependency direction preserved. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-004 | DECOMPOSITION.md SHA-256 d25daf16ceabfab852b7ed28e99b1f04f7b5d9d807bed2da517f48f053c6f9e7; Reviewed component interfaces/state additions and reset ownership; source inspect is nonconsuming, projection shared, timers belong to piece. All logical units have deterministic test seams. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-005 | SPEC.md SHA-256 0b31cc98d62e8cf165f5d44f76a0f0403567356509d2e274341fd3fb72af4740; E01–E04 and ROTATION child fully specified; SPEC/design recheck Ready, baseline contract scopes remain bounded. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-006 | PLAN.md SHA-256 0c6036b2c4de6d8ce51981194961187bf4b64ae45d4eb0d3a99077cbca75b2f5; Phase 2 appended with 2.1–2.3, 0.2.0 and explicit included/excluded contracts; strict Phase 1 prefix unchanged. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-007 | layout.md SHA-256 51692ccaa2b29020e85b99103506b948455ad7d3d152b0c98fdd2a670e294df8; Combined PLAN/layout review Ready; bounded core modules and consumer tests assigned. Scoped autoloop preapproval applies. |
| Pass 2 / DOCS-008 | TASKS.md SHA-256 54f7c6615d9dfa48a89a9fbea4e10a37e5c55e5a183cdab8329328bc541e1fee; T-007–T-013 appended under Phase 2; protected Phase 1 task/PLAN prefixes unchanged. Current task conformance Ready; next authorized reset to conditional visibility. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-002 | PROJECT.md SHA-256 91716f74c4e7067649eb99b4d3b96b813239984c4810595fb29b0a52421079eb; Conditional ghost scope adopted; accepted whole-piece midline decision applies. Other future scopes remain outside current product description. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-003 | ARCHITECTURE.md SHA-256 9c4237b24e91af4b32914048962c2d1b3efe4e5bd18af68bd6814dcaac1df0b6; Existing architecture reused unchanged: browser presentation decides what to draw from the core-owned projection; visibility never changes core movement. Reviewed current brief consistency; no new block/dependency required. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-004 | DECOMPOSITION.md SHA-256 d25daf16ceabfab852b7ed28e99b1f04f7b5d9d807bed2da517f48f053c6f9e7; Existing component decomposition reused unchanged: Board renderer owns ghost presentation and Landing projection supplies authoritative occupied cells. Visibility predicate is a bounded rendering function, not another state owner. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-005 | SPEC.md SHA-256 3a01580314e244aca554cd2faa6078aa14ff28d2ae3bc34f925dbddadcb9edb9; G01 specifies dynamic visibility and overrides only enhanced ghost presentation; SPEC/design recheck Ready. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-006 | PLAN.md SHA-256 7f32a9e05dd683e829571be8335dc4232ade3212562ea1e5ebced7cdba82ad8b; Phase 3 appended as a narrow visibility increment with explicit count rationale and version 0.3.0; earlier PLAN prefix unchanged. Scoped autoloop preapproval applies. |
| Pass 3 / DOCS-007 | layout.md SHA-256 51692ccaa2b29020e85b99103506b948455ad7d3d152b0c98fdd2a670e294df8; layout reused unchanged after current-scope review; existing renderer/unit/browser homes cover G01. Scoped autoloop preapproval applies. |

## Initialization and current reset

- Original root presence: retained AI_DISCLOSURE.md, SDD-MANAGER.md, AGENTS.md, SKILL_DISCOVERY_PROTOCOL.md; copied none. All compared to original Git blobs; preservation passed.
- Continuation: full initial set; no mixed-state continuation pause.
- Manual provenance: default enabled; two exact package assets seeded on design-docs/ai-logs at 073c9b0fd28f97648edfa0f1a5b6fe73f50c59bd from main 0e4dfbff6d3c6540e2300457878f12d32c131b4e, explicitly merged to main at cc0c042d00bb42f3171d16da27f03c4aedb794b8. Both branch commits pushed with matching remote readback. No transcript recorded. Branch retained.
- Tracking gate: sdd-docs-main milestone 1; DOCS-001–009 issues #1–#9, ordered, exact bundled brief content and milestone associations verified before writes. Standard definitions from repository-bundled SDD Manager v0.15.2.
- Git baseline: clean template main at 0e4dfbff6d3c6540e2300457878f12d32c131b4e; documentation branch created from published provenance merge cc0c042d00bb42f3171d16da27f03c4aedb794b8. Existing guidance and policies preserved.
- Credentials: supplied repository credential saved in conventional ignored/untracked file after projection, with effective exclusion verified. No values in tracked records.
- Current reset: 2 → 3; predefined autoloop scope Conditional ghost visibility; user first approval covers reset. Earlier PLAN/TASKS retained unchanged; same issues reopen after this reset publication.

## Pending transitions

Publish/read back the current Pass 3 DOCS-007 checkpoint and close/read back issue #7 with its exact commit evidence. Recover actual Git/hosted state before replay. Then prepare the next document in dependency order.
