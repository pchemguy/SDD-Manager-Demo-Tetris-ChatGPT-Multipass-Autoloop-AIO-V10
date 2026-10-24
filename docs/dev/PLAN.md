# Delivery plan

## Delivery strategy and gates

Deliver bounded playable increments against [SPEC](SPEC.md), with [ARCHITECTURE](ARCHITECTURE.md) and [DECOMPOSITION](DECOMPOSITION.md) defining ownership. Each phase preserves its predecessor's usable game except for explicitly included behavioral changes. A phase's verification is scoped to its included contracts; later contracts are not earlier-phase exit conditions.

Each delivery milestone ends with whole-capability code review, required checks and blocker repairs, a committed milestone report and HANDOFF update. Its final implementation task carries those obligations when no standalone milestone-review task is required. A standalone review task is used only when that milestone has at least four implementation tasks and the phase has at least two implementation milestones. Each phase also has a final special review milestone containing one phase review/testing/report task after all delivery milestones complete/close.

Use test-first core development and meaningful integration checks. Provision required isolated browsers early; diagnose failures under the browser policy without silently dropping required coverage. Before completing a phase, verify relevant regressions, static production build, documentation and demonstrated behavior. Integrate the complete verified phase with an explicit merge, verify the merged state and publish it before dependent phase activation. When implementation is authorized, publish the corresponding version/tag/release notes after each verified integrated phase. Compatible fixes increment patch versions.

## Phase 1 — Playable classic baseline

Outcome: a complete keyboard-controlled classic game, playable through game over and restart. Included contracts: B01–B05. Scope is the minimal coherent board/piece/gravity/clear/score/lifecycle experience; no independent gameplay enhancement is required for this phase. Dependency: accepted integrated preparation and required phase tracking before execution. Target version: 0.1.0.

### Milestone 1.1 — Playable classic game

Establish reproducible tooling and representative browser execution, then integrate geometry, session progression, browser input/rendering and readable status/restart into one usable game. A single delivery milestone avoids pretending separately coded layers are independently usable products. Demonstrate ordinary play, row clearing, game over and restart. Evidence must include deterministic edge/clear/progression checks, focus/suspension behavior, actual interaction/screenshots in both required browsers, production build and user setup/control instructions. Gather functional/usability feedback that permits continue/amend/simplify/stop before expanding scope; retain the selected authorized range.

Exit: B01–B05 acceptance, whole-milestone code review, blocker repairs, committed report at docs/dev/reports/phases/1/1.1.md and HANDOFF. Browser setup alone and mocked rendering do not satisfy playable acceptance.

### Milestone 1.2 — Phase review

After milestone 1.1 completion and hosted closure, review the complete baseline and all its exits, run integrated regressions and document remaining nonblocking TODOs. Retain docs/dev/reports/phases/1/PHASE-REPORT.md and HANDOFF. Verify phase scope, build/version readiness and integration prerequisites. Game functionality must remain usable after the verified published merge; release creation follows the authorized lifecycle.

## Phase 2 — Gameplay enhancements

Outcome: deliberate piece selection and landing assistance through Hold, 7-bag, wall kicks, ghost and hard drop with full-interval grounded locking. Included contracts: E01–E04 and relevant retained baseline behavior. Dependency: Phase 1 verified, integrated and published. Target version: 0.2.0. Conditional ghost visibility, penetrator behavior and settings are outside this phase's exits.

### Milestone 2.1 — Predictable piece control

Integrate shuffled bags, ordered wall kicks and Hold into the playable baseline. These controls share active-piece/source state and must remain usable together. Demonstrate an empty/nonempty Hold exchange, a seven-kind bag and wall/floor kick; gather clarity/availability feedback for continue/amend/simplify/stop within authorized scope. Exit: E01 and kick portion of E02, source-consumption/rejection/regression evidence, whole-milestone review/repairs, docs/dev/reports/phases/2/2.1.md and HANDOFF.

### Milestone 2.2 — Landing assistance and grounded timing

Integrate E03 interval timing with hard drop and ghost using one projection. Depends on 2.1 because Hold/rotation create the live-piece transitions the timing policy must handle. Demonstrate preview/drop agreement and visible full delay, including grounded rotation and repeated-drop cases. Exit: E02 projection and E03–E04, all milestone/retained baseline regressions, whole-capability review/repairs, docs/dev/reports/phases/2/2.2.md and HANDOFF.

### Milestone 2.3 — Phase review

After 2.1–2.2 complete/close, review the integrated enhanced game, browser interaction/screenshots, complete core timing/geometry/bag/hold coverage and production build. Retain docs/dev/reports/phases/2/PHASE-REPORT.md, TODOs and HANDOFF; verify version and release/integration readiness before the published complete-phase merge. Prior classic scoring, game-over/restart and focus guarantees remain applicable.

## Phase 3 — Conditional ghost visibility

Outcome: ghost assistance follows the complete-piece midline rule while gameplay remains unchanged. Included contract: G01 and relevant projection/rendering regressions. Dependency: Phase 2 verified, integrated and published. Target version: 0.3.0. Settings and penetrator behavior are outside this phase's exits.

### Milestone 3.1 — Midline-aware ghost

Implement the pure projected-cell visibility predicate in the renderer, integrate redraw and gather upper/lower/straddling screenshots. Demonstrate that hidden and visible ghosts have identical hard-drop destinations; use feedback on boundary clarity for continue/amend/simplify/stop within the authorized range. Exit: G01 fixtures and required-browser rendering evidence, enhanced gameplay regressions, whole-milestone review/repairs, docs/dev/reports/phases/3/3.1.md and HANDOFF. This deliberately narrow outcome needs one delivery milestone, without unrelated padding.

### Milestone 3.2 — Phase review

After 3.1 completes/closes, review visibility integration, regressions and build/version readiness. Retain docs/dev/reports/phases/3/PHASE-REPORT.md, TODOs and HANDOFF; verify complete-phase integration/release prerequisites while preserving Phase 2 gameplay.
